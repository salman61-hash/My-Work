```bash

<div class="position-relative passwordInput">
    <input type="password" name="password" id="password"
        class="form-control py-2 @error('password') is-invalid @enderror"
        placeholder="Password">
    <span class="eye" onclick="showHidePassword()">
        <i class="far fa-eye-slash" id="togglePassword"></i>
    </span>
</div>

<script>
function showHidePassword() {
    const password = document.getElementById("password");
    const toggle = document.getElementById("togglePassword");

    if (password.type === "password") {
        password.type = "text"; // Show password
        toggle.classList.remove("fa-eye-slash");
        toggle.classList.add("fa-eye"); // Open eye
    } else {
        password.type = "password"; // Hide password
        toggle.classList.remove("fa-eye");
        toggle.classList.add("fa-eye-slash"); // Closed eye
    }
}
</script>
```
