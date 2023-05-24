# chart-model
This project is developed by Sobhana Susil
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Inserting Image in Data Table</title>
    <link rel="stylesheet" type="text/css" href="https://cdn.datatables.net/1.11.4/css/jquery.dataTables.css">
    <style>
        input[type="text"] {
            width: 100%;
        }
        #dataTable th{
            background-color: black;
            color:wheat;
        }
        button{
            margin: 20px;
        }
        button:hover{
            background-color: aquamarine;
            border: none;
            color: blue;
        }
        #imgbox{
            background-color: antiquewhite;
            display: block;
            max-width: 300px;
        }
    </style>
</head>
<body>
    <input type="file" id="selectedImage" style="display: none;">
    <div id="imgbox">
        <img id="previewImage" src="" alt="" style="display: block; max-width: 300px; margin-bottom: 10px;">
    </div>
    <table id="dataTable">
        <thead>
            <tr>
                <th>Name</th>
                <th>Email</th>
                <th>Phone</th>
                <th>Address</th>
                <th>Position</th>
                <th>Image</th>
            </tr>
        </thead>
        <tbody>
            <tr>
                <td><input type="text" class="name-input"></td>
                <td><input type="text" class="email-input"></td>
                <td><input type="text" class="phone-input"></td>
                <td><input type="text" class="address-input"></td>
                <td><input type="text" class="position-input"></td>
                <td><input type="file" class="image-input" onchange="previewImage(event)"></td>
            </tr>
        </tbody>
    </table>
    <button onclick="storeData()">Save Data</button>
    <button onclick="Delete()">Delete</button>
    <button onclick="json()">Json file</button>
    <canvas id="chart" style="max-width: 600px; margin-top: 20px;"></canvas>
    <button onclick="displayChart()">Display Chart</button>

    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>

    <script src="https://code.jquery.com/jquery-3.6.0.min.js"></script>
    <script src="https://cdn.datatables.net/1.11.4/js/jquery.dataTables.js"></script>
    <script>
        $(document).ready(function() {
            $('#dataTable').DataTable();
        });

        function previewImage(event) {
            const selectedFile = event.target.files[0];
            const preview = document.getElementById('previewImage');

            const reader = new FileReader();
            reader.onload = function(event) {
                preview.src = event.target.result;
                preview.style.display = 'block';
            };

            reader.readAsDataURL(selectedFile);
        }

        function storeData() {
            const nameInput = document.querySelector('.name-input');
            const emailInput = document.querySelector('.email-input');
            const phoneInput = document.querySelector('.phone-input');
            const addressInput = document.querySelector('.address-input');
            const positionInput = document.querySelector('.position-input');
            const imageInput = document.querySelector('.image-input');

            const name = nameInput.value;
            const email = emailInput.value;
            const phone = phoneInput.value;
            const address = addressInput.value;
            const position = positionInput.value;
            const image = imageInput.files[0] ? imageInput.files[0].name : '';

            const data = {
                name: name,
                email: email,
                phone: phone,
                address: address,
                position: position,
                image: image
            };

            let storedData = localStorage.getItem('data');
            storedData = storedData ? JSON.parse(storedData) : [];
            storedData.push(data);
            localStorage.setItem('data', JSON.stringify(storedData));

            const tableBody = document.querySelector('#dataTable tbody');
            const newRow = document.createElement('tr');
            newRow.innerHTML = `
                <td>${name}</td>
                <td>${email}</td>
                <td>${phone}</td>
                <td>${address}</td>
                <td>${position}</td>
                <td>${image}</td>
            `;
            tableBody.appendChild(newRow);

            // Reset input values
            nameInput.value = '';
            emailInput.value = '';
            phoneInput.value = '';
            addressInput.value = '';
            positionInput.value = '';
            imageInput.value = '';

            // Hide preview image
            const preview = document.getElementById('previewImage');
            preview.src = '';
            preview.style.display = 'none';
        }

        function Delete(){
            localStorage.removeItem('data')
            alert("Data stored in local storage has been deleted.");
        }

        function json() {
            const storedData = localStorage.getItem('data');
            if (storedData) {
                const parsedData = JSON.parse(storedData);
                console.log(JSON.stringify(parsedData, null, 2));
            } else {
                console.log('No data stored.');
            }
        }
        function displayChart() {
    const storedData = localStorage.getItem('data');
    if (storedData) {
        const parsedData = JSON.parse(storedData);
        const positions = parsedData.map(entry => entry.position);

        const positionCounts = {};
        positions.forEach(position => {
            if (positionCounts[position]) {
                positionCounts[position]++;
            } else {
                positionCounts[position] = 1;
            }
        });

        const positionLabels = Object.keys(positionCounts);
        const positionData = Object.values(positionCounts);

        const ctx = document.getElementById('chart').getContext('2d');
        const chart = new Chart(ctx, {
            type: 'bar',
            data: {
                labels: positionLabels,
                datasets: [{
                    label: 'Position Distribution',
                    data: positionData,
                    backgroundColor: 'rgba(75, 192, 192, 0.6)',
                    borderColor: 'rgba(75, 192, 192, 1)',
                    borderWidth: 1
                }]
            },
            options: {
                responsive: true,
                scales: {
                    y: {
                        beginAtZero: true,
                        stepSize: 1
                    }
                }
            }
        });
    } else {
        console.log('No data stored.');
    }
}

    </script>
</body>
</html>

