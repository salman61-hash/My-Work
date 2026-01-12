Blade (Form)

```bash
  <form id="checkoutForm" action="{{ url('/api/boostplan/checkout') }}" method="POST">
                @csrf
                <div class="rs-checkout-body d-flex align-items-center justify-content-between fix">
                    <div class="rs-checkout-left">
                        <div class="rs-info-card">

                            <input type="hidden" name="boost_plan_id" value="{{ $boostPlan->id ?? '' }}">
                            <input type="hidden" name="selling_post_id" value="{{ $post->id ?? '' }}">
                            <input type="hidden" name="user_id" value="{{ $user->id ?? '' }}">
                            <div class="rs-checkout-form-top p-16 mb-25" data-bg-color="#F6F7F9">
                                <h5 class="rs-checkout-info-title">{{ __('User Information') }}</h5>
                                <div class="rs-checkout-input-item mb-16">
                                    <label class="rs-checkout-form-label">{{ __('Name') }}</label>
                                    <input class="rs-checkout-form-input" type="text" name="name"
                                        value="{{ old('name', auth()->user()->name) }}">
                                </div>
                                <div class="rs-checkout-input-item mb-16">
                                    <label class="rs-checkout-form-label">{{ __('Email') }}</label>
                                    <input class="rs-checkout-form-input" type="email" name="email"
                                        value="{{ old('email', auth()->user()->email) }}">
                                </div>
                                <div class="rs-checkout-input-item">
                                    <label class="rs-checkout-form-label">{{ __('Phone No') }}</label>
                                    <input class="rs-checkout-form-input" type="text" name="contact"
                                        value="{{ old('contact', auth()->user()->phone_no ?? '') }}">
                                </div>
                            </div>
                            <div class="rs-checkout-form-bottom p-16" data-bg-color="#F6F7F9" id="autoPayWrapper"
                                style="display:none;">
                                <div class="rs-checkout-autopay">
                                    <div class="rs-checkout-autopay-top mb-16">
                                        <h5 class="rs-checkout-autopay-title">{{ __('Auto Pay') }}</h5>
                                        <div class="rs-checkout-autopay-switch">
                                            <!-- Hidden input ensures 'false' is sent if checkbox is not checked -->
                                            <input type="hidden" name="auto_pay" value="0">

                                            <!-- Actual checkbox -->
                                            <input type="checkbox" name="auto_pay" id="autoPaySwitch" value="1"
                                                {{ old('auto_pay', false) ? 'checked' : '' }}>
                                            <label for="autoPaySwitch"></label>
                                        </div>
                                    </div>

                                    <p class="rs-checkout-autopay-text">
                                        {{ __('Thanks to Coerz\'soutstanding SEO services, we\'ve witnessed a remarkable improvement in our search engine rankings. Their expertise in keyword strategy and content optimization has led to a surge in organic traffic to our site.') }}
                                    </p>
                                </div>
                            </div>

                        </div>
                    </div>
                    <div class="rs-checkout-right">
                        <div class="rs-payment-summary">
                            <div class="rs-payment-header mb-16 fix">
                                <!-- Top: Days & Price -->
                                <div class="rs-payment-top d-flex align-items-center justify-content-between mb-8">
                                    <h4 class="rs-payment-days">
                                        <span
                                            id="display-days">{{ $boostPlan ? $boostPlan->sustain_days : 'Select Plan' }}</span>
                                        {{ __('Days') }}
                                    </h4>
                                    <div class="rs-payment-price" id="display-price">
                                        {{ $boostPlan
                                            ? ($boostPlan->discount_amount
                                                ? currencyFormat($boostPlan->discount_amount)
                                                : currencyFormat($boostPlan->payable_amount))
                                            : '0.00' }}

                                    </div>
                                </div>

                                <!-- Bottom: Name & Dropdown -->
                                <div class="rs-payment-bottom d-flex align-items-center justify-content-between">
                                    <h5 class="rs-payment-title-2" id="display-name">
                                        {{ $boostPlan ? $boostPlan->name : 'Select Plan' }}
                                    </h5>

                                    <!-- Dropdown -->
                                    <div class="rs-payment-change p-relative">
                                        <select id="plan" name="boost_plan_id"
                                            class="rs-payment-change-item-select">
                                            <option value="">{{ __('Change Item') }}</option>
                                            @foreach ($allBoostPlans as $plan)
                                                <option value="{{ $plan->id }}"
                                                    data-days="{{ $plan->sustain_days }}"
                                                    data-price="{{ $plan->discount_amount ?? $plan->payable_amount }}"
                                                    data-name="{{ $plan->name }}"
                                                    {{ $boostPlan && $boostPlan->id == $plan->id ? 'selected' : '' }}>
                                                    {{ $plan->sustain_days }} Day{{ $plan->sustain_days > 1 ? 's' : '' }}
                                                    -
                                                    {{ $plan->discount_amount ? number_format($plan->discount_amount, 2) : number_format($plan->payable_amount, 2) }}
                                                </option>
                                            @endforeach

                                        </select>
                                    </div>

                                </div>
                            </div>

                            <div class="rs-checkout-right-center">
                                <div class="rs-service-summary" data-tax-type="{{ $taxData->type ?? 'percentage' }}"
                                    data-tax-value="{{ $taxData->value ?? 0 }}">
                                    <h5 class="rs-service-summary-title">{{ __('Service Summary') }}</h5>

                                    <div class="rs-summary-col p-16">
                                        <div class="rs-summary-row d-flex align-items-center justify-content-between">
                                            <span>{{ __('Service Price') }}</span>
                                            <input type="hidden" name="service_price" id="service-price-hidden"
                                                value="{{ $boostPlan ? $boostPlan->discount_amount ?? $boostPlan->payable_amount : 0 }}">
                                            <span id="service-price" name="service-price">
                                                {{ $boostPlan ? ($boostPlan->discount_amount ? number_format($boostPlan->discount_amount, 2) : number_format($boostPlan->payable_amount, 2)) : '0.00' }}
                                            </span>
                                        </div>

                                        <div class="rs-summary-row tex d-flex align-items-center justify-content-between">
                                            <input type="hidden" name="service_tax" id="service-tax-hidden"
                                                value="0">

                                            <span>
                                                {{ __('Tax ') }}
                                                (
                                                @if (($taxData->type ?? 'percentage') === 'percentage')
                                                    {{ $taxData->value ?? 0 }}%
                                                @else
                                                    {{ number_format($taxData->value ?? 0, 2) }}
                                                @endif
                                                )
                                            </span>



                                            <span id="service-tax" name="service-tax">0.00</span>
                                        </div>
                                        <div
                                            class="rs-summary-row total d-flex align-items-center justify-content-between">
                                            <input type="hidden" name="service_total" id="service-total-hidden"
                                                value="{{ $boostPlan ? $boostPlan->payable_amount : 0 }}">
                                            <span>{{ __('Total') }}</span>
                                            <span id="service-total"
                                                name="service-total">{{ $boostPlan ? number_format($boostPlan->payable_amount, 2) : '0.00' }}</span>
                                        </div>
                                    </div>
                                </div>

                                <div class="rs-checkout-payment-method p-16">
                                    <h5 class="rs-checkout-payment-method-title">{{ __('Payment Method') }}</h5>

                                    {{-- Hidden input to store selected payment method ID --}}
                                    <input type="hidden" name="payment_gateway_id" id="payment_gateway_id">
                                    <input type="hidden" id="is_wallet" name="is_wallet" value="0">

                                    <div class="rs-method-buttons gap-16 mb-3">
                                        @foreach ($paymentGateways as $gateway)
                                            @if ($gateway->is_active)
                                                <button type="button" class="rs-payment-method-img-btn payment-btn"
                                                    data-id="{{ $gateway->id }}">
                                                    <img src="{{ $gateway->imagePath }}"
                                                        alt="{{ ucfirst($gateway->name) }}">
                                                </button>
                                            @endif
                                        @endforeach
                                    </div>
                                    <div class="text-center mb-3 fw-bold">Or</div>
                                    <!-- Wallet Button -->
                                    <button type="button"
                                        class="btn-primary btn-lg w-100 d-flex align-items-center justify-content-center gap-2 payment-btn rs-payment-method-img-btn payment-btn"
                                        data-id="null" id="walletBtn">
                                        <i class="fas fa-wallet me-2"></i> {{ __('Use Wallet') }}
                                    </button>

                                </div>
                                <div class="rs-payment-actions d-flex align-items-center justify-content-between gap-16 p-16"
                                    data-bg-color="#F6F7F9">
                                    <button type="button" class="rs-payment-cancel">{{ __('Cancel ') }}</button>
                                    <button type="submit" class="rs-payment-proceed rs-btn" id="submitBtn">
                                        <span class="btn-icon">
                                            <i class="bi bi-credit-card"></i>
                                        </span>

                                        <span class="btn-text">
                                            {{ __('Proceed Payment') }}
                                        </span>

                                        <span class="btn-loader d-none">
                                            <span class="spinner-border spinner-border-sm me-2"></span>
                                            Processing...
                                        </span>
                                    </button>

                                </div>

                            </div>

                            <div class="rs-note-box mt-16" id="paypleNoteBox" style="display: none;">
                                <span>{{ __('Note') }}<b>*</b></span>
                                <div class="bg-light border rounded p-3 mt-3 small">

                                    <div class="alert alert-warning border-start border-warning border-5 mt-3">
                                        <i class="fas fa-info-circle me-2"></i>
                                        <strong>{{ __('PayPal Currency Info') }}:</strong>
                                        @if (
                                            !in_array(strtoupper(env('CURRENCY')), [
                                                'USD',
                                                'EUR',
                                                'GBP',
                                                'AUD',
                                                'CAD',
                                                'JPY',
                                                'CHF',
                                                'SEK',
                                                'NOK',
                                                'DKK',
                                                'PLN',
                                                'CZK',
                                                'HUF',
                                                'ILS',
                                                'MXN',
                                                'BRL',
                                                'MYR',
                                                'PHP',
                                                'TWD',
                                                'THB',
                                                'NZD',
                                                'SGD',
                                                'HKD',
                                                'CNY',
                                            ]))
                                            <span class="text-danger">
                                                {{ __(' PayPal does not support') }}
                                                <strong>{{ strtoupper(env('CURRENCY')) }}</strong>.
                                                {{ __('Please use') }} <strong>{{ __('Wallet') }}</strong>
                                                {{ __('or another gateway') }}.
                                            </span>
                                        @else
                                            {{ __('PayPal supports your currency') }}
                                            (<strong>{{ strtoupper(env('CURRENCY')) }}</strong>)
                                        @endif
                                    </div>
                                </div>
                            </div>
                        </div>
                    </div>


            </form>

            ```
Javascript for form submit and popUp window

```bash
<script>
            $(document).ready(function() {
                const successModal = document.querySelector('#successModal');

                const openPaymentPopupWindow = (url, debug = false) => {
                    if (debug) console.log(`Opening payment window: ${url}`);

                    const winWidth = 700;
                    const winHeight = 700;
                    const left = screen.width / 2 - winWidth / 2;
                    const top = screen.height / 2 - winHeight / 2;

                    const options = `resizable,height=${winHeight},width=${winWidth},top=${top},left=${left}`;
                    const win = window.open(url, "_blank", options);

                    if (!win) {
                        if (debug) console.error("Failed to open payment window");
                        alert("Popup blocked! Please allow popups.");
                        return;
                    }

                    win.document.title = "Payment Window Screen - Make Payment";

                    let intervalID = null;

                    const handleWindowClose = (status) => {
                        if (debug) console.log(`Closing popup with status: ${status}`);
                        if (win && !win.closed) {
                            win.close();
                        }
                    };

                    const trackWindow = () => {
                        try {
                            if (win.closed) {
                                if (debug) console.log("Window manually closed");
                                clearInterval(intervalID);
                                handleWindowClose("closed");
                                return;
                            }

                            const currentHost = location.host;

                            // Only check if same-origin
                            if (win.location.host === currentHost) {
                                const pathname = win.location.pathname;

                                if (pathname.includes("payment/success")) {
                                    clearInterval(intervalID);
                                    handleWindowClose("success");
                                    successModal.style.display = 'block';
                                } else if (pathname.includes("payment/cancel")) {
                                    clearInterval(intervalID);
                                    handleWindowClose("cancel");
                                } else if (pathname.includes("payment/fail")) {
                                    clearInterval(intervalID);
                                    handleWindowClose("fail");
                                    Swal.fire({
                                        icon: "error",
                                        title: "Payment Failed!",
                                        text: "Your payment process was unsuccessful. Please try again.",
                                    });
                                }
                            }

                        } catch (e) {
                            if (debug) console.warn(
                                "Cross-origin access blocked; cannot track status until redirect returns to same-origin."
                            );
                        }
                    };

                    intervalID = setInterval(trackWindow, 100); // check every 100ms
                    win.focus();
                };

                $("#checkoutForm").on("submit", function(e) {
                    e.preventDefault();

                    const form = $(this);
                    const actionUrl = form.attr("action");

                    $.ajax({
                        type: "POST",
                        url: actionUrl,
                        headers: {
                            'Accept': 'application/json',
                            'X-CSRF-TOKEN': $('meta[name="csrf-token"]').attr('content')
                        },
                        data: form.serialize(),
                        dataType: "json",
                        success: function(response) {

                            if (response.data == "null") {
                                Swal.fire({
                                    icon: "error",
                                    title: "Payment Failed!",
                                    text: `${response.message}`,
                                });
                                return;
                            }


                            if (response.data.payment_url) {
                                openPaymentPopupWindow(response.data.payment_url, true);
                                return;
                            }


                            if (response.message) {
                                successModal.style.display = 'block';
                            }
                        },

                        error: function(xhr) {
                            console.log(xhr.responseText);
                        }
                    });
                });

            });



            function closeModal() {
                successModal.style.display = 'none';
                errorModal.style.display = 'none';

            }
            document.querySelectorAll('.rs-payment-popup-close, .actionBtn').forEach(btn => {
                btn.addEventListener('click', closeModal);
            });

            overlay.addEventListener('click', closeModal);
        </script>

```

Controller

```bash
    public function checkoutApi(Request $request)
    {
        
        if (!$request->payment_gateway_id && !$request->is_wallet) {
            return $this->json('Please select payment method', null , 422);
        }
        $transactionIdentifier = substr(str_shuffle(str_repeat('0123456789abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ', 16)), 0, 16);

        $user = Auth::user() ?? Auth::guard('sanctum')->user();


        $data = [
            'boost_plan_id' => $request->boost_plan_id,
            'selling_post_id' => $request->selling_post_id,
            'payment_gateway_id' => $request->payment_gateway_id,
            'user_id' => $request?->user_id ?? $user->id,
            'name' => $request->name ?? $user?->name,
            'email' => $request->email ?? $user?->email,
            'contact' => $request->contact ?? $user?->phone_no,
            'auto_pay' => $request->auto_pay ?? false,
            'service_price' => $request->service_price,
            'service_tax' => $request->service_tax,
            'service_total' => $request->service_total ?? $request->amount,
            'identifier' => $transactionIdentifier,
        ];
        // Store via Repository
        $transaction = TransactionRepository::storeByRequest($data);

        //  If wallet, handle immediately
        if ($request->is_wallet == 1) {
            return $this->walletManagement($transactionIdentifier);
        }

        return $this->json('checkout success', [
            'payment_url' => route('payment.view', $transactionIdentifier)
        ], 200);
    }
```
Wallet Management

```bash

    public function walletManagement($identifier)
    {

        // Find the transaction
        $transaction = TransactionRepository::query()->where('identifier', $identifier)->first();
        if (!$transaction) {
            return back()->withError('Transaction not found');
        }

        // Find user's wallet
        $wallet = WalletRepository::query()->where('user_id', $transaction->user_id)->first();

        // dd($wallet);
        if (!$wallet || $wallet->balance < $transaction->amount) {
            return $this->json('Insufficient wallet balance.','null',200);
            // return back()->withError('Insufficient wallet balance.');
        }

        // Deduct the amount
        $wallet->balance -= $transaction->amount;
        $wallet->save();

        // Record wallet transaction
        $walletTxn = WalletTransactionRepository::create([
            'wallet_id' => $wallet->id,
            'type' => 'debit',
            'amount' => $transaction->amount,
            'transaction_id' => $transaction->identifier,
            'note' => 'Payment deducted for purchase',
            'balance_after' => $wallet->balance,
        ]);

        // Mark transaction as paid
        $transaction->update([
            'is_paid' => true,
            'status' => 'complete',
            'paid_at' => now(),
            'wallet_transaction_id' => $walletTxn->id
        ]);




        $boostPlan = $transaction->boostPlan;

        BoostPostRepository::query()->firstOrCreate(
            ['selling_post_id' => $transaction->selling_post_id],
            [
                'boost_plan_id' => $transaction->boost_plan_id,
                'auto_pay' => $transaction->auto_pay,
                'expired_at' => $boostPlan ? now()->addDays($boostPlan->sustain_days) : null,
            ]
        );

        $user = $transaction->user;
        $senderId = $user->id;

        // Admin Notification
        $receiverIds = null;
        $senderId =  $senderId;
        $adminSubject = "Product Boosted by" . $user->name;
        $adminBody = $user->name . " has boosted a product. An amount of " . currencyFormat($transaction->amount) . " has been added to your account.";


        NotifyManagementEvent::dispatch(
            $receiverIds,
            $senderId,
            $adminSubject,
            $adminBody
        );

        // User Notification
        $receiverIds = $user->id;
        $senderId =  $senderId;
        $subject = "Your product has been Boosted..!!";
        $body = "Your product has been successfully boosted. An amount of " . currencyFormat($transaction->amount) . " has been deducted from your wallet.";


        NotifyManagementEvent::dispatch(
            $receiverIds,
            $senderId,
            $subject,
            $body
        );

        return $this->json('Payment successful! Amount deducted from your wallet.', 200);
    }
```
Payment View

```bash
    public function paymentView($identifier)
    {

        $transaction = TransactionRepository::query()->where('identifier', $identifier)->first();
        $this->paymentService->processPayment($transaction->amount, [
            'gateway' => $transaction->payment_method->name ?? 'N/A',
            'identifier' => base64_encode($transaction->identifier),
            'product' => [
                'product' => $transaction->sellingPost->name ?? 'N/A',
                'price' => $transaction->amount,
                'images' => $transaction->course->mediaPath ?? 'https://placehold.co/600x400'
            ],
            'customer' => [
                'name' => $transaction->user?->name ?? 'N/A',
                'email' => $transaction->user?->email ?? 'N/A',
                'phone' => $transaction->user?->phone ?? 'N/A',
            ]
        ]);
    }
```
PaymentService.php(Head of All payment service)

```bash

<?php

namespace App\Services;

use App\Models\PaymentGateway;

class PaymentService
{
    public function processPayment($amount, array $data)
    {
        $gateway = PaymentGateway::where('name', $data['gateway'])->firstOrFail();
        if ($gateway->is_active == 0) {
            return redirect()->back()->with('error', 'Payment gateway is not active');
        }

        // $config = json_decode($gateway->config, true);
        $config = is_string($gateway->config) ? json_decode($gateway->config, true) : $gateway->config;
        switch ($gateway->name) {
            case 'stripe':
                return (new StripePayment())->processPayment($amount, $data, $config);
            case 'aamarpay':
                return (new AamarPayment())->processPayment($amount, $data, $config);
            default:
                throw new \Exception("Unsupported payment gateway");
        }
    }


    public function paymentView()
    {
        $gateway = PaymentGateway::where('is_active', 1)->firstOrFail();
        switch ($gateway->name) {
            case 'stripe':
                return view('payment.stripe.stripe');
            case 'aamarpay':
                return view('payment.sslcommerz');
            default:
                throw new \Exception("Unsupported payment gateway");
        }
    }
}

```

Payment Gateway Interface

```bash
<?php

namespace App\Interfaces;

interface PaymentGatewayInterface
{
    public function processPayment($amount, array $data, array $config);
}
```
