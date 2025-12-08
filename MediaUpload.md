```bash
Relation

 public function thumbnail()
    {
        return $this->belongsTo(Media::class, 'thumbnail_id');
    }
```

```bash

Media Repository

    public static function storeByRequest($fileInputName, $folderName = 'default')
    {
        $file = $fileInputName;
        $path = Storage::disk('public')->put('/' . trim($folderName, '/'), $file);
        $extension = $file->extension();

        return self::create([
            'original_name' => $file->getClientOriginalName(),
            'src'           => $path,
            'type'          => $file->getClientMimeType(),
            'extension'     => $extension,
            'added_by'      => Auth::id(),
        ]);
    }

```

```bash
Controller

 public function store(BoostPlanRequest $request)
    {
        $data = $request->except('_token');

        if (isset($data['plan_items'])) {
            $data['plan_items'] = json_encode($data['plan_items']);
        }
         if ($request->hasFile('thumbnail')) {
            $media = MediaRepository::storeByRequest($request->file('thumbnail'), 'thumbnails');
            $data['thumbnail_id'] = $media->id;
        }

        BoostPlanRepository::create($data);

        return redirect()->route('plans.index')->with('success', 'Boost Plan created successfully.');
    }

```

```bash

Blade for create

 <div class="mb-3">
                <label class="form-label">{{ __('Thumbnail') }}</label>
                <input type="file" name="thumbnail" class="form-control">
            </div>

```

```bash

Blade For Update with img preview

 {{-- Thumbnail --}}
            <div class="mb-3">
                <label class="form-label">{{ __('Thumbnail') }}</label>
                <input type="file" name="thumbnail" id="thumbnailInput" class="form-control">

                {{-- Preview --}}
                <div class="mt-3" id="thumbnailPreview">
                    @if ($plan->thumbnail)
                        <p class="mb-1">{{ __('Current Image:') }}</p>
                        <img src="{{ asset('storage/' . $plan->thumbnail->src) }}" alt="Thumbnail" width="50"
                            height="50" class="rounded">
                    @endif
                </div>
            </div>


        <script>
            const thumbnailInput = document.getElementById('thumbnailInput');
            const thumbnailPreview = document.getElementById('thumbnailPreview');

            thumbnailInput.addEventListener('change', function(e) {
                const file = e.target.files[0];
                if (!file) return;

                // Remove old preview
                thumbnailPreview.innerHTML = '';

                // Create new image preview
                const img = document.createElement('img');
                img.src = URL.createObjectURL(file);
                img.width = 50;
                img.height = 50;
                img.classList.add('rounded');

                thumbnailPreview.appendChild(img);
            });
        </script>

```
