Payple Service

```bash
<?php

namespace App\Services;

use App\Interfaces\PaymentGatewayInterface;
use PaypalServerSdkLib\PaypalServerSdkClientBuilder;
use PaypalServerSdkLib\Authentication\ClientCredentialsAuthCredentialsBuilder;
use PaypalServerSdkLib\Orders\OrdersCreateRequest;
use PaypalServerSdkLib\Environment;

class PaypalServerPayment implements PaymentGatewayInterface
{
    public function processPayment($amount, array $data, array $configInput)
    {
        $environment = ($configInput['mode'] ?? 'sandbox') === 'live'
            ? Environment::PRODUCTION
            : Environment::SANDBOX;

        $client = PaypalServerSdkClientBuilder::init()
            ->clientCredentialsAuthCredentials(
                ClientCredentialsAuthCredentialsBuilder::init(
                    $configInput['client_id'] ?? '',
                    $configInput['client_secret'] ?? ''
                )
            )
            ->environment($environment)
            ->build();

        $order = [
            'intent' => 'CAPTURE',
            'purchase_units' => [[
                'amount' => [
                    'currency_code' => $configInput['currency'] ?? 'USD',
                    'value' => number_format($amount, 2, '.', ''),
                ],
            ]],
            'application_context' => [
                'return_url' => route('paypal.payment.success', $data['identifier']),
                'cancel_url' => route('paypal.payment.cancel'),
            ],
        ];

        try {
            // $response = $client->getOrdersController()->createOrder($request);
            $response = $client->getOrdersController()->createOrder(['body' => $order]);

            $orderModel = $response->getResult();

            $orderAsArray = json_decode(json_encode($orderModel), true);

            // Now $orderAsArray is a clean nested PHP array

            foreach ($orderAsArray['links'] as $link) {
                if ($link['rel'] === 'approve') {
                    return redirect()->away($link['href']);
                }
            }

            return response()->json(['message' => 'Approval link not found'], 400);
        } catch (\Exception $e) {
            return response()->json(['error' => $e->getMessage()], 400);
        }
    }
}
```
AmarPay Service

```bash
<?php

namespace App\Services;

use App\Interfaces\PaymentGatewayInterface;
use Exception;

class AamarPayment implements PaymentGatewayInterface
{
    /**
     * Process the payment using Aamar Payment Gateway.
     *
     * @param float $amount The amount to be paid.
     * @param array $data Additional data for the payment.
     * @param array $config Configuration for the payment.
     */
    public function processPayment($amount, array $data, array $config)
    {

        // Generate a unique transaction ID
        $tran_id = $data['identifier'];

        // Set the currency to use (BDT for Bangladeshi Taka)
        $currency = 'BDT';

        // Store ID provided by Aamar Payment
        $store_id = $config['store_id'];

        // Signature key provided by Aamar Payment
        $signature_key = $config['signature_key'];

        // URL to send the payment request
        $url = "https://sandbox.aamarpay.com/jsonpost.php";

        // Initialize cURL
        $curl = curl_init();
        // Set cURL options
        curl_setopt_array(
            $curl,
            array(
                CURLOPT_URL => $url,
                CURLOPT_RETURNTRANSFER => true,
                CURLOPT_ENCODING => "",
                CURLOPT_MAXREDIRS => 10,
                CURLOPT_TIMEOUT => 30,
                CURLOPT_HTTP_VERSION => CURL_HTTP_VERSION_1_1,
                CURLOPT_CUSTOMREQUEST => "POST",
                CURLOPT_FOLLOWLOCATION => true,
                CURLOPT_POSTFIELDS => '{' .
                    '"store_id": "' . $store_id . '",' .
                    '"tran_id": "' . $tran_id . '",' .
                    '"success_url": "' . route('aamrpay.payment.success') . '",' .
                    '"fail_url": "' . route('aamrpay.payment.fail') . '",' .
                    '"cancel_url": "' . route('aamrpay.payment.cancel') . '",' .
                    '"amount": "' . $amount . '",' .
                    '"currency": "' . $currency . '",' .
                    '"signature_key": "' . $signature_key . '",' .
                    '"desc": "Merchant Registration Payment",' .
                    '"cus_name": "' . $data['customer']['name'] . '",' .
                    '"cus_email": "' . $data['customer']['email'] . '",' .
                    '"cus_add1": "N/A",' .
                    '"cus_add2": "N/A",' .
                    '"cus_city": "N/A",' .
                    '"cus_state": "N/A",' .
                    '"cus_postcode": "0000",' .
                    '"cus_country": "Bangladesh",' .
                    '"cus_phone": "' . $data['customer']['phone'] . '",' .
                    '"type": "json"' .
                    '}',
                CURLOPT_HTTPHEADER => array(
                    'Content-Type: application/json'
                ),
            )
        );

        // Execute the cURL request and get the response
        $response = curl_exec($curl);

        // Close the cURL session
        curl_close($curl);

        // Decode the JSON response
        $responseObj = json_decode($response);

        // If a payment URL is returned, redirect the user to it
        if (isset($responseObj->payment_url) && !empty($responseObj->payment_url)) {

            $paymentUrl = $responseObj->payment_url;
            return redirect($paymentUrl);
        } else {
            // Otherwise, echo the response
            echo $response;
        }
    }
}
```
Bkash Service

```bash
<?php

namespace App\Services;

use App\Interfaces\PaymentGatewayInterface;
use App\Models\PaymentGateway;
use App\Repositories\TransactionRepository;

class BkashPayment implements PaymentGatewayInterface
{
    public function processPayment($amount, array $data, $config)
    {
        $paymentGateway = PaymentGateway::where('name', 'bkash')->first();
        $token = $paymentGateway->token;
        $status = '0000';

        if (!$token || !$paymentGateway->token_expires_at || $paymentGateway->token_expires_at < now()) {
            $response = json_decode(self::tokenGrant($config));
            $generateExpiredTime = now()->addMinutes(59);
            $paymentGateway->update([
                'token' => $response->id_token,
                'token_expires_at' => $generateExpiredTime,
            ]);
            $status = $response->statusCode;
            $token = $response->id_token;
        }

        $transactionId = $data['product']['transaction_id'];

        $transaction = TransactionRepository::query()->where('id', $transactionId)->first();

        $transaction->update([
            'token' => $token,
        ]);

        if ($status != '0000') {
            return json_encode(['error' => $status]);
        }

        $callbackUrl = route('bkash.payment.execute', ['data' => $data['identifier'], 'token' => $token]);

        $url = 'https://tokenized.sandbox.bka.sh/v1.2.0-beta/tokenized/checkout/create';

        if ($config['mode'] == 'live') {
            $url = 'https://tokenized.pay.bka.sh/v1.2.0-beta/tokenized/checkout/create';
        }

        $requestbody = [
            'mode' => '0011',
            'amount' => $amount,
            'currency' => 'BDT',
            'intent' => 'sale',
            'payerReference' => '01',
            'merchantInvoiceNumber' => 'TRX' . str_pad($transactionId, 6, '0', STR_PAD_LEFT) . '-' . rand(1111, 9999),
            'callbackURL' => $callbackUrl,
        ];

        $header = [
            'Content-Type:application/json',
            'Accept:application/json',
            'Authorization:' . $token,
            'X-APP-Key:' . $config['app_key'],
        ];

        $resultData = self::executeCurl($url, $requestbody, $header);

        $resultData = json_decode($resultData);

        if (isset($resultData->bkashURL)) {
            return  redirect()->away($resultData->bkashURL);
        }

        // error handling
        return json_encode(['error' => $resultData]);
    }

    /**
     * bKash token grant
     *
     * @return string
     */
    private static function tokenGrant($config)
    {
        $url = 'https://tokenized.sandbox.bka.sh/v1.2.0-beta/tokenized/checkout/token/grant';

        if ($config['mode'] == 'live') {
            $url = 'https://tokenized.pay.bka.sh/v1.2.0-beta/tokenized/checkout/token/grant';
        }

        $authorizableData = [
            'app_key' => $config['app_key'],
            'app_secret' => $config['app_secret_key'],
        ];

        $header = [
            'Content-Type:application/json',
            'Accept:application/json',
            'username:' . $config['username'],
            'password:' . $config['password'],
        ];

        return self::executeCurl($url, $authorizableData, $header);
    }

    /**
     * bKash payment execute eCurl
     *
     * @return string
     */
    private static function executeCurl($url, $data, $header)
    {
        $curl = curl_init($url);
        curl_setopt($curl, CURLOPT_HTTPHEADER, $header);
        curl_setopt($curl, CURLOPT_CUSTOMREQUEST, 'POST');
        curl_setopt($curl, CURLOPT_RETURNTRANSFER, true);
        curl_setopt($curl, CURLOPT_POSTFIELDS, json_encode($data));
        curl_setopt($curl, CURLOPT_FOLLOWLOCATION, 1);
        curl_setopt($curl, CURLOPT_IPRESOLVE, CURL_IPRESOLVE_V4);

        $result = curl_exec($curl);

        curl_close($curl);

        return $result;
    }
}
```
Stripe Service

```bash
<?php

namespace App\Services;

use App\Interfaces\PaymentGatewayInterface;
use Stripe\Stripe;

class StripePayment implements PaymentGatewayInterface
{
    public function processPayment($amount, array $data, array $config)
    {
        Stripe::setApiKey($config['secret_key']);
        $session = \Stripe\Checkout\Session::create([
            'line_items' => [
                [
                    'price_data' => [
                        'currency' => config('app.currency'),
                        'product_data' => ['name' => $data['product']['product']],
                        'unit_amount' => $amount * 100,
                    ],
                    'quantity' => 1,
                ]
            ],
            'mode' => 'payment',
            'success_url' => route('stripe.payment.success', $data['identifier']),
            'cancel_url' => route('stripe.payment.cancel'),
        ], ['api_key' => $config['secret_key']]);
        return redirect($session->url);
    }
}
```

Razor Service

```bash

<?php

namespace App\Services;

use App\Interfaces\PaymentGatewayInterface;
use App\Models\PaymentGateway;
use Razorpay\Api\Api;

class RazorPayment implements PaymentGatewayInterface
{

    public function processPayment($amount, array $data,  array $configInput)
    {

        $razorpay = new Api($configInput['key'], $configInput['secret']);

        try {
            $paymentLink = $razorpay->invoice->create([
                'type' => 'link',
                'amount' => $amount * 100, // amount in paisa
                'currency' => config('app.currency'),
                'description' => $data['product']['product'],
                'customer' => [
                    'name' => $data['customer']['name'],
                    'email' => $data['customer']['email'],
                    'contact' => $data['customer']['phone'],
                ],
                'callback_url' => route('razorpay.payment.success', $data['identifier']),
                'redirect' => true,
                'callback_method' => 'get',
                'cancel_url' => route('razorpay.payment.fail'),
            ]);

            $url = $paymentLink['short_url'];

            return redirect()->away($url);
        } catch (\Razorpay\Api\Errors\SignatureVerificationError $e) {
            // Handle signature verification error
            return response()->json(['error' => $e->getMessage()], 400);
        } catch (\Exception $e) {
            // Handle other exceptions
            return response()->json(['error' => $e->getMessage()], 500);
        }
    }
}
```
