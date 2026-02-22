```bash

<script>
document.addEventListener('DOMContentLoaded', function() {
    if (navigator.geolocation) {
        navigator.geolocation.getCurrentPosition(function(position) {
            const lat = position.coords.latitude;
            const lng = position.coords.longitude;

            // Page reload with lat/lng query params
            if (!window.location.search.includes('lat')) { // avoid infinite reload
                window.location.href = `/?lat=${lat}&lng=${lng}`;
            }
        });
    }
});
</script>

```
