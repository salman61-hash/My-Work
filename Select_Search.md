Step 1: CDN Add
```bash
<!-- Select2 CSS -->
<link href="https://cdn.jsdelivr.net/npm/select2@4.1.0-rc.0/dist/css/select2.min.css" rel="stylesheet" />

<!-- jQuery (if not already added) -->
<script src="https://code.jquery.com/jquery-3.6.0.min.js"></script>

<!-- Select2 JS -->
<script src="https://cdn.jsdelivr.net/npm/select2@4.1.0-rc.0/dist/js/select2.min.js"></script>
```
Step 2: Update Html
```bash
<select class="form-control searchable-store" name="store">
    <option value="">{{ __('All Stores') }}</option>
    @foreach ($stores as $store)
        <option value="{{ $store->id }}"
            {{ $store->id == request('store') ? 'selected' : '' }}>
            {{ $store->name }}
        </option>
    @endforeach
</select>
```
Step 3: JS
```bash
<script>
    $(document).ready(function() {
        $('.searchable-store').select2({
            placeholder: "Select Store",
            allowClear: true,
            width: '200px'
        });
    });
</script>
