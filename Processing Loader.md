Input Field

```bash
                                                  <span class="btn-loader d-none">
                                                        <span class="spinner-border spinner-border-sm"></span>
                                                        Processing...
                                                    </span>
```

Javascript

```bash
 const btn = document.getElementById('submitBtn');
            btn.querySelector('.btn-text').classList.add('d-none');
            btn.querySelector('.btn-icon').classList.add('d-none');
            btn.querySelector('.btn-loader').classList.remove('d-none');
```
