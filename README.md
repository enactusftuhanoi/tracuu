<!DOCTYPE html>
<html lang="vi">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Trang Thông Tin Thành Viên</title>
    <link rel="icon" href="icon.png" type="image/png">
    <style>
        body {
            font-family: 'Montserrat', sans-serif;
            background-color: #f8f9fa;
            margin: 0;
            padding: 0;
        }
        .container {
            width: 80%;
            margin: 50px auto;
            background-color: #ffffff;
            padding: 30px;
            border-radius: 10px;
            box-shadow: 0 4px 10px rgba(0, 0, 0, 0.1);
        }
        h2 {
            color: #0057b8;
            text-align: center;
            margin-bottom: 30px;
        }
        .profile-info {
            width: 100%;
            border-collapse: collapse;
            margin-bottom: 20px;
        }
        .profile-info th, .profile-info td {
            padding: 15px;
            text-align: left;
            border: 1px solid #d9d9d9;
        }
        .profile-info th {
            background-color: #ffc222;
            color: white;
        }
        .profile-info td {
            background-color: #d9d9d9;
        }
        .profile-info img {
            border-radius: 50%;
            width: 50px;
            height: 50px;
            object-fit: cover;
        }
        .btn-edit {
            background-color: #0057b8;
            color: white;
            padding: 8px 15px;
            border: none;
            border-radius: 5px;
            cursor: pointer;
        }
        .btn-edit:hover {
            background-color: #357ae8;
        }
        .form-group {
            margin-bottom: 15px;
        }
        .form-group input {
            width: 100%;
            padding: 10px;
            border: 1px solid #ccc;
            border-radius: 5px;
            background-color: #f8f9fa;
        }
        .form-group input:disabled {
            background-color: #e9ecef;
        }
        .form-group button {
            background-color: #ffc222;
            color: white;
            padding: 10px 20px;
            border: none;
            border-radius: 5px;
            cursor: pointer;
        }
        .form-group button:hover {
            background-color: #ffb020;
        }
        .btn-save {
            background-color: #28a745;
        }
        .btn-save:hover {
            background-color: #218838;
        }
    </style>
</head>
<body>
    <div class="container">
        <h2>Thông Tin Thành Viên</h2>

        <!-- Profile Info -->
        <table class="profile-info">
            <thead>
                <tr>
                    <th>Ảnh đại diện</th>
                    <th>Email</th>
                    <th>Họ và Tên</th>
                    <th>Ngày Sinh</th>
                    <th>Số điện thoại</th>
                    <th>Ban</th>
                    <th>Quy trình</th>
                    <th>Thao tác</th>
                </tr>
            </thead>
            <tbody id="profile-table-body">
                <!-- Thông tin sẽ được hiển thị ở đây -->
            </tbody>
        </table>

        <!-- Edit Form -->
        <form id="edit-form" style="display: none;">
            <div class="form-group">
                <label for="name">Họ và Tên</label>
                <input type="text" id="name" disabled>
            </div>
            <div class="form-group">
                <label for="dob">Ngày Sinh</label>
                <input type="date" id="dob" disabled>
            </div>
            <div class="form-group">
                <label for="phone">Số Điện Thoại</label>
                <input type="text" id="phone" disabled>
            </div>
            <div class="form-group">
                <label for="email">Email</label>
                <input type="email" id="email" disabled>
            </div>
            <div class="form-group">
                <label for="profile-picture">Ảnh Đại Diện</label>
                <input type="file" id="profile-picture" disabled>
            </div>
            <div class="form-group">
                <button type="button" class="btn-save" onclick="saveProfile()">Lưu</button>
            </div>
        </form>
    </div>

    <script type="module">
        import { initializeApp } from "https://www.gstatic.com/firebasejs/10.8.0/firebase-app.js";
        import { getAuth, onAuthStateChanged } from "https://www.gstatic.com/firebasejs/10.8.0/firebase-auth.js";
        import { getFirestore, doc, getDoc } from "https://www.gstatic.com/firebasejs/10.8.0/firebase-firestore.js";

        const firebaseConfig = {
            apiKey: "AIzaSyDuTvBn8Xl01DYddVXQ7M0L24K3l-GyG0c",
            authDomain: "enactusftuhanoi-tracuu.firebaseapp.com",
            projectId: "enactusftuhanoi-tracuu",
            storageBucket: "enactusftuhanoi-tracuu.firebasestorage.app",
            messagingSenderId: "611356979403",
            appId: "1:611356979403:web:2c9a4cffb2b323ce3deb4e"
        };

        // Khởi tạo Firebase
        const app = initializeApp(firebaseConfig);
        const auth = getAuth(app);
        const db = getFirestore(app);

        onAuthStateChanged(auth, async (user) => {
            if (user) {
                console.log("User logged in: ", user);
                const email = user.email;
                const userRef = doc(db, "employees", email); // Lấy thông tin người dùng từ Firestore
                const docSnap = await getDoc(userRef);

                if (docSnap.exists()) {
                    const userData = docSnap.data();
                    console.log("User data from Firestore: ", userData);
                    displayUserProfile(userData);
                } else {
                    alert("Không tìm thấy thông tin người dùng trong Firestore!");
                }
            } else {
                window.location.href = "login.html"; // Chuyển hướng đến trang đăng nhập nếu chưa đăng nhập
            }
        });

        function displayUserProfile(userData) {
            const tableBody = document.getElementById("profile-table-body");

            const row = document.createElement("tr");
            row.innerHTML = `
                <td><img src="${userData.profilePicture || 'https://via.placeholder.com/150'}" alt="Profile Picture"></td>
                <td>${userData.email}</td>
                <td contenteditable="true">${userData.name || 'Chưa cập nhật'}</td>
                <td contenteditable="true">${userData.dob || 'Chưa cập nhật'}</td>
                <td contenteditable="true">${userData.phone || 'Chưa cập nhật'}</td>
                <td>${userData.ban || 'Chưa cập nhật'}</td>
                <td>${userData.process || 'Chưa cập nhật'}</td>
                <td><button class="btn-edit" onclick="editProfile()">Sửa</button></td>
            `;
            tableBody.appendChild(row);
        }

        function editProfile() {
            const table = document.querySelector(".profile-info");
            const row = table.rows[1]; // Lấy hàng đầu tiên
            const cells = row.getElementsByTagName("td");

            document.getElementById("name").value = cells[2].innerText;
            document.getElementById("dob").value = cells[3].innerText;
            document.getElementById("phone").value = cells[4].innerText;
            document.getElementById("email").value = cells[1].innerText;
            document.getElementById("profile-picture").value = cells[0].getElementsByTagName("img")[0].src;

            document.getElementById("edit-form").style.display = 'block';
        }

        function saveProfile() {
            // Lấy thông tin từ form sửa và lưu lại
            alert("Thông tin đã được cập nhật!");
            // Cập nhật lại Firestore nếu cần
        }
    </script>
</body>
</html>
