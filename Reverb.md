### Building a User-to-Admin Chat System Using Laravel Reverb

এই গাইডে আমরা Laravel Reverb ব্যবহার করে একটা রিয়েল-টাইম চ্যাট সিস্টেম তৈরি করব, যেখানে ইউজার অ্যাডমিনের সাথে সরাসরি যোগাযোগ করতে পারবে। এটা একটা প্রাইভেট চ্যানেল-ভিত্তিক সিস্টেম হবে, যাতে শুধুমাত্র অথেনটিকেটেড ইউজার এবং অ্যাডমিন মেসেজ পাঠাতে/পড়তে পারে।

আমরা ধাপে ধাপে এগোবো, Laravel-এর অফিসিয়াল ডকুমেন্টেশন স্টাইলে। এর জন্য Laravel 11.x বা তার উপরের ভার্সন দরকার (Reverb Laravel 11-এ ইন্ট্রোডিউস হয়েছে)। আমি কোড স্নিপেট, কমান্ড এবং ব্যাখ্যা দিব।

#### প্রয়োজনীয়তা (Prerequisites)
- **Laravel ইনস্টলেশন:** একটা নতুন বা বিদ্যমান Laravel প্রজেক্ট (laravel new chat-app)।
- **ডাটাবেস:** MySQL বা PostgreSQL সেটআপ করা (migration চালানোর জন্য)।
- **Node.js & NPM:** ফ্রন্টএন্ডের জন্য (Laravel Echo ইনস্টল করতে)।
- **Composer:** Laravel প্যাকেজ ইনস্টল করতে।
- **Queue Worker:** Reverb broadcasting queued job-এর উপর নির্ভর করে, তাই `php artisan queue:work` চালানোর সুবিধা।
- **Authentication:** Laravel Breeze বা Jetstream ইনস্টল করুন যাতে ইউজার লগিন/রেজিস্টার থাকে। (যেমন: `php artisan breeze:install` এবং `npm install && npm run dev`)।
- **বেসিক জ্ঞান:** Laravel Events, Broadcasting, Eloquent Models, Blade/Vue/React.

যদি আপনার প্রজেক্টে authentication না থাকে, প্রথমে সেটা সেটআপ করুন। আমরা ধরে নিব যে `User` মডেলে একটা `is_admin` কলাম আছে (0/1) যাতে অ্যাডমিন আলাদা করা যায়।

#### ধাপ 1: Laravel Reverb ইনস্টল করা (Installing Reverb)
Reverb হচ্ছে Laravel-এর first-party WebSocket সার্ভার, যা Pusher-এর মতো কাজ করে কিন্তু ফ্রি এবং সেল্ফ-হোস্টেড।

1. **ইনস্টল কমান্ড চালান:**
   ```bash
   php artisan install:broadcasting --reverb
   ```
   - এটা Reverb প্যাকেজ ইনস্টল করবে (Composer & NPM দিয়ে)।
   - `.env` ফাইলে Reverb-এর ভ্যারিয়েবল যোগ করবে (যেমন: `REVERB_APP_ID`, `REVERB_APP_KEY` ইত্যাদি)।
   - Broadcasting চালু করবে `config/broadcasting.php`-এ।
   - `routes/channels.php` তৈরি করবে চ্যানেল অথরাইজেশনের জন্য।

2. **.env ফাইল চেক করুন:**
   ```env
   BROADCAST_CONNECTION=reverb
   REVERB_APP_ID=your-app-id  # যেকোনো ইউনিক ID
   REVERB_APP_KEY=your-key    # সিক্রেট কী
   REVERB_APP_SECRET=your-secret
   REVERB_HOST=127.0.0.1     # লোকালহোস্ট
   REVERB_PORT=8080
   REVERB_SCHEME=http
   VITE_REVERB_APP_KEY="${REVERB_APP_KEY}"
   VITE_REVERB_HOST="${REVERB_HOST}"
   VITE_REVERB_PORT="${REVERB_PORT}"
   VITE_REVERB_SCHEME="${REVERB_SCHEME}"
   ```

3. **Reverb সার্ভার চালান:**
   ```bash
   php artisan reverb:start
   ```
   - প্রোডাকশনে Supervisor বা অন্য ডেমন ব্যবহার করুন যাতে এটা ব্যাকগ্রাউন্ডে চলে।

#### ধাপ 2: ডাটাবেস সেটআপ (Database Setup)
চ্যাটের জন্য একটা `messages` টেবিল দরকার, যেখানে ইউজার থেকে অ্যাডমিনের মেসেজ স্টোর হবে।

1. **Message মডেল ও মাইগ্রেশন তৈরি করুন:**
   ```bash
   php artisan make:model Message -m
   ```

2. **Migration ফাইলে কলাম যোগ করুন (`database/migrations/..._create_messages_table.php`):**
   ```php
   public function up(): void
   {
       Schema::create('messages', function (Blueprint $table) {
           $table->id();
           $table->foreignId('sender_id')->constrained('users')->onDelete('cascade');  // ইউজার যে পাঠিয়েছে
           $table->foreignId('receiver_id')->constrained('users')->onDelete('cascade');  // অ্যাডমিন বা অন্য
           $table->text('message');
           $table->timestamps();
       });
   }
   ```

3. **মাইগ্রেট করুন:**
   ```bash
   php artisan migrate
   ```

4. **Message মডেলে রিলেশন যোগ করুন (`app/Models/Message.php`):**
   ```php
   namespace App\Models;

   use Illuminate\Database\Eloquent\Factories\HasFactory;
   use Illuminate\Database\Eloquent\Model;

   class Message extends Model
   {
       use HasFactory;

       protected $fillable = ['sender_id', 'receiver_id', 'message'];

       public function sender()
       {
           return $this->belongsTo(User::class, 'sender_id');
       }

       public function receiver()
       {
           return $this->belongsTo(User::class, 'receiver_id');
       }
   }
   ```

5. **User মডেলে isAdmin মেথড যোগ করুন (`app/Models/User.php`):**
   ```php
   public function isAdmin(): bool
   {
       return $this->is_admin === 1;  // ধরে নিলাম is_admin কলাম আছে
   }
   ```

#### ধাপ 3: চ্যাট ইভেন্ট তৈরি করা (Defining the Chat Event)
Reverb broadcasting-এর জন্য একটা ইভেন্ট দরকার যা মেসেজ পাঠানোর সময় ব্রডকাস্ট হবে।

1. **ইভেন্ট তৈরি করুন:**
   ```bash
   php artisan make:event MessageSent
   ```

2. **ইভেন্ট ক্লাস এডিট করুন (`app/Events/MessageSent.php`):**
   ```php
   namespace App\Events;

   use App\Models\Message;
   use Illuminate\Broadcasting\Channel;
   use Illuminate\Broadcasting\InteractsWithSockets;
   use Illuminate\Broadcasting\PrivateChannel;
   use Illuminate\Contracts\Broadcasting\ShouldBroadcast;
   use Illuminate\Foundation\Events\Dispatchable;
   use Illuminate\Queue\SerializesModels;

   class MessageSent implements ShouldBroadcast
   {
       use Dispatchable, InteractsWithSockets, SerializesModels;

       public $message;

       public function __construct(Message $message)
       {
           $this->message = $message;
       }

       public function broadcastOn(): array
       {
           // প্রাইভেট চ্যানেল: user.{userId}.admin.{adminId}
           return [
               new PrivateChannel('chat.' . min($this->message->sender_id, $this->message->receiver_id) . '.' . max($this->message->sender_id, $this->message->receiver_id)),
           ];
       }

       public function broadcastAs(): string
       {
           return 'message.sent';
       }

       public function broadcastWith(): array
       {
           return [
               'id' => $this->message->id,
               'sender_id' => $this->message->sender_id,
               'message' => $this->message->message,
               'created_at' => $this->message->created_at->toDateTimeString(),
           ];
       }
   }
   ```
   - **broadcastOn:** প্রাইভেট চ্যানেল যাতে শুধু ইউজার এবং অ্যাডমিন অ্যাক্সেস করতে পারে। min/max দিয়ে চ্যানেল নাম ইউনিক রাখা হয়েছে।
   - **broadcastAs:** ক্লায়েন্টে ইভেন্টের নাম।
   - **broadcastWith:** কোন ডেটা পাঠানো হবে।

#### ধাপ 4: চ্যানেল অথরাইজেশন (Authorizing Channels)
`routes/channels.php`-এ চ্যানেলের অথরাইজেশন লজিক যোগ করুন।

```php
Broadcast::channel('chat.{id1}.{id2}', function ($user, $id1, $id2) {
    // চেক করুন ইউজার এই চ্যাটের অংশ কি না
    return $user->id == $id1 || $user->id == $id2;
});
```
- এটা নিশ্চিত করে যে শুধুমাত্র সেন্ডার/রিসিভার চ্যানেলে লিসেন করতে পারবে।

#### ধাপ 5: ব্যাকএন্ড লজিক (Backend: Handling Messages)
1. **কন্ট্রোলার তৈরি করুন:**
   ```bash
   php artisan make:controller ChatController
   ```

2. **ChatController এডিট করুন (`app/Http/Controllers/ChatController.php`):**
   ```php
   namespace App\Http\Controllers;

   use App\Events\MessageSent;
   use App\Models\Message;
   use App\Models\User;
   use Illuminate\Http\Request;
   use Illuminate\Support\Facades\Auth;

   class ChatController extends Controller
   {
       public function index()
       {
           // অ্যাডমিন লিস্ট বা চ্যাট হিস্ট্রি লোড করুন
           $admins = User::where('is_admin', 1)->get();
           return view('chat', compact('admins'));
       }

       public function sendMessage(Request $request)
       {
           $request->validate([
               'message' => 'required|string',
               'receiver_id' => 'required|exists:users,id',
           ]);

           $message = Message::create([
               'sender_id' => Auth::id(),
               'receiver_id' => $request->receiver_id,
               'message' => $request->message,
           ]);

           // ইভেন্ট ডিসপ্যাচ: এটা ব্রডকাস্ট হবে
           broadcast(new MessageSent($message))->toOthers();

           return response()->json(['status' => 'Message sent!']);
       }

       public function getMessages($receiverId)
       {
           // চ্যাট হিস্ট্রি লোড
           $messages = Message::where(function ($query) use ($receiverId) {
               $query->where('sender_id', Auth::id())->where('receiver_id', $receiverId);
           })->orWhere(function ($query) use ($receiverId) {
               $query->where('sender_id', $receiverId)->where('receiver_id', Auth::id());
           })->orderBy('created_at')->get();

           return response()->json($messages);
       }
   }
   ```

3. **রাউট যোগ করুন (`routes/web.php`):**
   ```php
   Route::middleware('auth')->group(function () {
       Route::get('/chat', [ChatController::class, 'index'])->name('chat.index');
       Route::post('/chat/send', [ChatController::class, 'sendMessage']);
       Route::get('/chat/messages/{receiverId}', [ChatController::class, 'getMessages']);
   });
   ```

#### ধাপ 6: ফ্রন্টএন্ড সেটআপ (Frontend: Using Laravel Echo)
1. **Echo কনফিগার করুন (`resources/js/bootstrap.js`):**
   ```js
   import Echo from 'laravel-echo';
   import Pusher from 'pusher-js';

   window.Pusher = Pusher;

   window.Echo = new Echo({
       broadcaster: 'reverb',
       key: import.meta.env.VITE_REVERB_APP_KEY,
       wsHost: import.meta.env.VITE_REVERB_HOST,
       wsPort: import.meta.env.VITE_REVERB_PORT ?? 80,
       wssPort: import.meta.env.VITE_REVERB_PORT ?? 443,
       forceTLS: (import.meta.env.VITE_REVERB_SCHEME ?? 'https') === 'https',
       enabledTransports: ['ws', 'wss'],
   });
   ```

2. **অ্যাসেট কম্পাইল করুন:**
   ```bash
   npm run dev
   ```

3. **চ্যাট ভিউ তৈরি করুন (`resources/views/chat.blade.php`):**
   ```blade
   @extends('layouts.app')

   @section('content')
       <div id="chat-app">
           <h1>User to Admin Chat</h1>
           <select id="admin-select">
               @foreach($admins as $admin)
                   <option value="{{ $admin->id }}">{{ $admin->name }}</option>
               @endforeach
           </select>
           <div id="messages"></div>
           <input type="text" id="message-input" placeholder="Type your message...">
           <button id="send-button">Send</button>
       </div>

       <script>
           const userId = {{ Auth::id() }};
           let adminId = document.getElementById('admin-select').value;

           // চ্যানেল নাম: chat.minId.maxId
           const channelName = `chat.${Math.min(userId, adminId)}.${Math.max(userId, adminId)}`;

           // হিস্ট্রি লোড
           function loadMessages() {
               fetch(`/chat/messages/${adminId}`)
                   .then(response => response.json())
                   .then(messages => {
                       document.getElementById('messages').innerHTML = messages.map(msg => `<p>${msg.sender_id === userId ? 'You' : 'Admin'}: ${msg.message}</p>`).join('');
                   });
           }

           loadMessages();

           // রিয়েল-টাইম লিসেন
           Echo.private(channelName)
               .listen('.message.sent', (e) => {
                   document.getElementById('messages').innerHTML += `<p>${e.sender_id === userId ? 'You' : 'Admin'}: ${e.message}</p>`;
               });

           // সেন্ড বাটন
           document.getElementById('send-button').addEventListener('click', () => {
               const message = document.getElementById('message-input').value;
               fetch('/chat/send', {
                   method: 'POST',
                   headers: {
                       'Content-Type': 'application/json',
                       'X-CSRF-TOKEN': '{{ csrf_token() }}'
                   },
                   body: JSON.stringify({ message, receiver_id: adminId })
               }).then(() => {
                   document.getElementById('message-input').value = '';
               });
           });

           // অ্যাডমিন চেঞ্জ হলে রিলোড
           document.getElementById('admin-select').addEventListener('change', (e) => {
               adminId = e.target.value;
               loadMessages();
           });
       </script>
   @endsection
   ```

#### ধাপ 7: টেস্টিং এবং ডিবাগিং (Testing)
1. **কুয়ু ওয়ার্কার চালান:** `php artisan queue:work`
2. **Reverb চালান:** `php artisan reverb:start`
3. **ব্রাউজারে চেক করুন:** লগিন করে /chat পেজে যান। মেসেজ পাঠান, দেখুন রিয়েল-টাইম আপডেট হয় কি না।
4. **ডিবাগ:** `config/broadcasting.php`-এ 'log' ড্রাইভার ব্যবহার করে লগ চেক করুন।

#### অতিরিক্ত ফিচার (Advanced)
- **প্রেজেন্স চ্যানেল:** অ্যাডমিন অনলাইন কি না দেখানোর জন্য PresenceChannel ব্যবহার করুন।
- **টাইপিং ইন্ডিকেটর:** একটা নতুন ইভেন্ট যোগ করে।
- **প্রোডাকশন:** Reverb-কে HTTPS-এ চালান, এবং Horizon বা অন্য কুয়ু ম্যানেজার ব্যবহার করুন।
- **সিকিউরিটি:** Rate limiting যোগ করুন মেসেজ সেন্ডে।
