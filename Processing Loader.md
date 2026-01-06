Input Field

```bash
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
```

Javascript

```bash
<script>
            document.addEventListener('DOMContentLoaded', function() {

                const btn = document.getElementById('submitBtn');
                if (!btn) return;

                const form = btn.closest('form');
                if (!form) return;

                form.addEventListener('submit', function() {

                    btn.querySelector('.btn-text').classList.add('d-none');
                    btn.querySelector('.btn-icon').classList.add('d-none');
                    btn.querySelector('.btn-loader').classList.remove('d-none');
                    setTimeout(() => {
                        btn.querySelector('.btn-text').classList.remove('d-none');
                        btn.querySelector('.btn-icon').classList.remove('d-none');
                        btn.querySelector('.btn-loader').classList.add('d-none');
                    }, 2000);


                });

            });
        </script>
```
