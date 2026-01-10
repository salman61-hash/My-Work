Create Email-only event & listener
```bash
php artisan make:event SendCustomMailEvent
php artisan make:listener SendCustomMailListener --queued
```

app/Events/SendCustomMailEvent.php
```bash
<?php

namespace App\Events;

use App\Models\User;
use Illuminate\Broadcasting\Channel;
use Illuminate\Broadcasting\InteractsWithSockets;
use Illuminate\Broadcasting\PresenceChannel;
use Illuminate\Broadcasting\PrivateChannel;
use Illuminate\Contracts\Broadcasting\ShouldBroadcast;
use Illuminate\Foundation\Events\Dispatchable;
use Illuminate\Queue\SerializesModels;

class SendCustomMailEvent
{
    use Dispatchable, InteractsWithSockets, SerializesModels;

    public $user;
    public $title;
    public $body;

    public function __construct(User $user, string $title, string $body)
    {
        $this->user = $user;
        $this->title = $title;
        $this->body = $body;
    }

    /**
     * Get the channels the event should broadcast on.
     *
     * @return array<int, \Illuminate\Broadcasting\Channel>
     */
    public function broadcastOn(): array
    {
        return [
            new PrivateChannel('channel-name'),
        ];
    }
}

```
app/Events/SendCustomMailListener.php
```bash
<?php

namespace App\Listeners;

use App\Events\SendCustomMailEvent;
use App\Mail\SendCustomMail;
use Illuminate\Contracts\Queue\ShouldQueue;
use Illuminate\Queue\InteractsWithQueue;
use Illuminate\Support\Facades\Log;
use Illuminate\Support\Facades\Mail;

class SendCustomMailListener implements ShouldQueue
{
    use InteractsWithQueue;

    /**
     * Create the event listener.
     */
    public function __construct()
    {
        //
    }

    /**
     * Handle the event.
     */
    public function handle(SendCustomMailEvent $event)
    {
        $user = $event->user;

        try {
            Mail::to($user->email)->send(new SendCustomMail($event->title, $event->body));
        } catch (\Exception $e) {
            Log::error("Mail send failed for user {$user->id}: " . $e->getMessage());
        }
    }
}
```
app/Mail/SendCustomMail

```bash
<?php

namespace App\Mail;

use Illuminate\Bus\Queueable;
use Illuminate\Contracts\Queue\ShouldQueue;
use Illuminate\Mail\Mailable;
use Illuminate\Mail\Mailables\Content;
use Illuminate\Mail\Mailables\Envelope;
use Illuminate\Queue\SerializesModels;
use Illuminate\Mail\Mailables\Address;

class SendCustomMail extends Mailable implements ShouldQueue
{
    use Queueable, SerializesModels;

    /**
     * Create a new message instance.
     */
    public $title;
    public $body;

    public function __construct($title, $body)
    {
        $this->title = $title;
        $this->body = $body;
    }

    /**
     * Get the message envelope.
     */
    public function envelope(): Envelope
    {
        return new Envelope(
            subject: $this->title,
            from: new Address(config('mail.mailers.smtp.username'), config('app.name')),
        );
    }

    /**
     * Get the message content definition.
     */
    public function content(): Content
    {
        return new Content(
            view: 'notification.custom.customMail',
            with: [
                'title' => $this->title,
                'body' => $this->body,
            ],
        );
    }

    /**
     * Get the attachments for the message.
     *
     * @return array<int, \Illuminate\Mail\Mailables\Attachment>
     */
    public function attachments(): array
    {
        return [];
    }
}

```

resources/views/notification/custom/customMail.blade.php

```bash
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>{{ $title ?? 'Notification from ' . config('app.name') }}</title>

    <style>
        body {
            margin: 0;
            padding: 0;
            background-color: #f3f4f6;
            font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Arial, sans-serif;
            color: #1f2937;
        }

        .wrapper {
            width: 100%;
            padding: 40px 15px;
        }

        .email-container {
            max-width: 600px;
            margin: 0 auto;
            background: #ffffff;
            border-radius: 14px;
            overflow: hidden;
            box-shadow: 0 10px 30px rgba(0,0,0,0.08);
        }

        /* Header */
        .header {
            background: linear-gradient(135deg, #2563eb, #1e40af);
            padding: 35px 30px;
            text-align: center;
        }

        .header h1 {
            margin: 0;
            color: #ffffff;
            font-size: 26px;
            font-weight: 700;
            letter-spacing: 0.3px;
        }

        /* Content */
        .content {
            padding: 35px 30px;
            line-height: 1.7;
            font-size: 16px;
        }

        .content p {
            margin: 0 0 20px;
            color: #374151;
        }

        /* Divider */
        .divider {
            height: 1px;
            background: #e5e7eb;
            margin: 30px 0;
        }

        /* Button */
        .button-wrapper {
            text-align: center;
            margin-top: 30px;
        }

        .button {
            display: inline-block;
            padding: 14px 26px;
            background-color: #2563eb;
            color: #ffffff !important;
            text-decoration: none;
            border-radius: 8px;
            font-weight: 600;
            font-size: 15px;
        }

        .button:hover {
            background-color: #1e40af;
        }

        /* Footer */
        .footer {
            background: #f9fafb;
            padding: 25px;
            text-align: center;
            font-size: 13px;
            color: #6b7280;
        }

        .footer strong {
            color: #374151;
        }

        /* Mobile */
        @media (max-width: 600px) {
            .content {
                padding: 25px 20px;
            }

            .header h1 {
                font-size: 22px;
            }
        }
    </style>
</head>
<body>

<div class="wrapper">
    <div class="email-container">

        <!-- Header -->
        <div class="header">
            <h1>{{ config('app.name') }}</h1>
        </div>

        <!-- Body -->
        <div class="content">
            {!! $body !!}

            @if(isset($actionText) && isset($actionUrl))
                <div class="button-wrapper">
                    <a href="{{ $actionUrl }}" target="_blank" class="button">
                        {{ $actionText }}
                    </a>
                </div>
            @endif

            <div class="divider"></div>

            <p style="font-size:14px;color:#6b7280;">
                If you did not expect this message, you can safely ignore this email.
            </p>
        </div>

        <!-- Footer -->
        <div class="footer">
            © {{ date('Y') }} <strong>{{ config('app.name') }}</strong><br>
            All rights reserved.
        </div>

    </div>
</div>

</body>
</html>
```

Dispatch in SendMessage Controller

```bash
 SendCustomMailEvent::dispatch($client, $title, $message);
```

.env set up

```bash
MAIL_MAILER="smtp"
MAIL_HOST="smtp.gmail.com"
MAIL_SCHEME=null
MAIL_PORT="587"
MAIL_ENCRYPTION="ssl"
MAIL_USERNAME="lms.razinsoft@gmail.com"
MAIL_PASSWORD="nhhyxoaowzyhxopv"
MAIL_FROM_ADDRESS="lms.razinsoft@gmail.com"
MAIL_FROM_NAME="Travel Promotion"
```

Run Command

```bash
php artisan queue:table
php artisan migrate
php artisan make:mail SendMail
```


