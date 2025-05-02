# roofreport

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Online Roof Inspection Report Generator</title>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf/2.5.1/jspdf.umd.min.js"></script>
    <style>
        /* ... (keep existing styles) ... */
        .location-section { display: flex; gap: 10px; }
        #getLocationBtn { background: #607D8B; }
    </style>
</head>
<body>
    <h1>Roof Inspection Report Generator</h1>
    
    <div class="section">
        <h3>Property Information</h3>
        <!-- Add Location Section -->
        <div class="location-section">
            <input type="text" id="location" placeholder="Property Location" required style="flex: 1;">
            <button type="button" id="getLocationBtn" onclick="getLocation()">📍 Get Current Location</button>
        </div>
        <!-- Keep existing inputs -->
        <input type="text" id="address" placeholder="Property Address" required>
        <!-- ... rest of existing inputs ... -->
    </div>

    <!-- ... rest of existing HTML ... -->

    <script>
        // Add these functions for location handling
        function getLocation() {
            if (navigator.geolocation) {
                navigator.geolocation.getCurrentPosition(
                    showPosition,
                    handleLocationError,
                    { enableHighAccuracy: true, timeout: 5000, maximumAge: 0 }
                );
            } else {
                alert("Geolocation is not supported by this browser.");
            }
        }

        async function showPosition(position) {
            try {
                const response = await fetch(
                    `https://nominatim.openstreetmap.org/reverse?format=json&lat=${position.coords.latitude}&lon=${position.coords.longitude}`
                );
                const data = await response.json();
                const address = data.address || {};
                const location = [
                    address.road,
                    address.neighbourhood,
                    address.city,
                    address.state,
                    address.country
                ].filter(Boolean).join(', ');
                
                document.getElementById('location').value = location || "Location found but could not get address details";
            } catch (error) {
                console.error('Geocoding error:', error);
                document.getElementById('location').value = `Lat: ${position.coords.latitude.toFixed(4)}, Lon: ${position.coords.longitude.toFixed(4)}`;
            }
        }

        function handleLocationError(error) {
            switch(error.code) {
                case error.PERMISSION_DENIED:
                    alert("User denied the request for Geolocation.");
                    break;
                case error.POSITION_UNAVAILABLE:
                    alert("Location information is unavailable.");
                    break;
                case error.TIMEOUT:
                    alert("The request to get user location timed out.");
                    break;
                default:
                    alert("An unknown error occurred.");
                    break;
            }
        }

        // Modify the generatePDF function to include location
        async function generatePDF() {
            const pdf = new jsPDF();
            
            // Add location to the report
            pdf.setFontSize(18);
            pdf.text("ROOF INSPECTION REPORT", 105, 20, { align: 'center' });
            
            pdf.setFontSize(12);
            let y = 40;
            
            // New location field
            pdf.text(`Location: ${document.getElementById('location').value}`, 20, y);
            y += 10;
            
            // Existing fields
            pdf.text(`Address: ${document.getElementById('address').value}`, 20, y);
            y += 10;
            // ... rest of existing PDF generation code ...
        }

        // ... rest of existing JavaScript ...
    </script>
</body>
</html>
