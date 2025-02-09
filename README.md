
<!DOCTYPE html>
<html lang="th">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>สั่งซื้อสินค้า</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0-beta3/css/all.min.css">
    <style>
        body {
            font-family: Arial, sans-serif;
            background-color: #002aff;
            color: white;
            margin: 0;
            padding: 0;
            transition: background 0.3s;
        }

        header {
            background-color: #001f99;
            padding: 10px;
            text-align: center;
            position: sticky;
            top: 0;
            z-index: 100;
        }

        nav ul {
            padding: 0;
            margin: 0;
            list-style: none;
        }

        nav ul li {
            display: inline;
            margin: 0 10px;
        }

        nav ul li a {
            color: white;
            text-decoration: none;
            font-size: 18px;
            padding: 5px 10px;
        }

        main {
            padding: 30px;
            text-align: center;
        }

        .orderPage h1 {
            font-size: 2em;
        }

        .orderPage button {
            background-color: #00ffcc;
            color: white;
            padding: 10px 20px;
            border: none;
            font-size: 18px;
            cursor: pointer;
            border-radius: 5px;
        }

        #adminBtn {
            position: fixed;
            top: 10px;
            right: 10px;
            padding: 10px 15px;
            background: red;
            border: none;
            color: white;
            cursor: pointer;
            transition: transform 0.3s;
            border-radius: 10px; /* มุมโค้ง */
        }

        #adminBtn:hover {
            transform: scale(1.1);
        }

        .admin-panel {
            display: none;
            background: white;
            color: black;
            padding: 20px;
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100vh;
            overflow-y: auto;
            border-radius: 0; /* แสดงเต็มจอ */
            box-shadow: 0px 0px 10px rgba(0, 0, 0, 0.5);
        }

        .close {
            float: right;
            cursor: pointer;
            font-size: 20px;
            color: red;
        }

        .product-item {
            background: white;
            color: black;
            padding: 10px;
            margin: 5px;
            display: inline-block;
            border-radius: 5px;
        }

        .delete-btn {
            background: red;
            color: white;
            border: none;
            cursor: pointer;
            margin-left: 10px;
        }

        .price-input {
            width: 50px;
            margin-left: 5px;
        }

        /* แอนิเมชัน Fade In สำหรับ Pop-up */
        .popup {
            display: none;
            position: fixed;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            background-color: white;
            padding: 30px;
            border-radius: 10px;
            box-shadow: 0px 0px 10px rgba(0, 0, 0, 0.5);
            animation: fadeIn 0.5s ease-out forwards;
            width: 400px;
            height: 500px;
        }

        @keyframes fadeIn {
            from {
                opacity: 0;
                transform: translate(-50%, -60%);
            }
            to {
                opacity: 1;
                transform: translate(-50%, -50%);
            }
        }

        /* ปุ่มปิด Pop-up */
        .popup .close-popup {
            float: right;
            cursor: pointer;
            font-size: 20px;
            color: red;
        }

        /* ฟังก์ชันเพิ่มอนิเมชั่นให้ปุ่ม */
        .btn-animate {
            transition: all 0.3s ease;
        }

        .btn-animate:hover {
            transform: scale(1.1);
            background-color: #ff6600;
        }

        .confirm-btn {
            background-color: #00ffcc;
            color: white;
            padding: 10px 20px;
            border: none;
            font-size: 18px;
            cursor: pointer;
            border-radius: 5px;
            transition: transform 0.3s, background-color 0.3s;
        }

        .confirm-btn:hover {
            transform: scale(1.1);
            background-color: #009999;
        }

        .delete-btn:hover {
            transform: scale(1.1);
            background-color: #ff3333;
        }
    </style>
</head>
<body>

<header>
    <nav>
        <ul>
            <li><a href="javascript:void(0);" onclick="showSignup()">สมัครสมาชิก</a></li>
            <li><a href="javascript:void(0);">เมนู</a></li>
            <li><a href="javascript:void(0);">เกี่ยวกับเรา</a></li>
        </ul>
    </nav>
    <button id="adminBtn" onclick="toggleAdminPanel()">จัดการหลังร้าน</button>
</header>

<main>
    <div class="orderPage">
        <h1>สั่งซื้อสินค้า</h1>
        <p>เลือกสินค้าและคลิกที่ปุ่มเพื่อสั่งซื้อ</p>
        <button onclick="showPaymentInfo()">สั่งซื้อ</button>
        <div id="productList"></div> 
    </div>
</main>

<div id="adminPanel" class="admin-panel">
    <span class="close" onclick="toggleAdminPanel()">&times;</span>
    <h2>ระบบจัดการหลังร้าน</h2>

    <h3>เพิ่มสินค้า</h3>
    <input type="text" id="productName" placeholder="ชื่อสินค้า">
    <input type="number" id="productPrice" placeholder="ราคา" min="1">
    <button onclick="addProduct()" class="btn-animate">เพิ่ม</button>

    <h3>รายการสินค้า</h3>
    <ul id="adminProductList"></ul>

    <h3>เปลี่ยนเบอร์ Wallet</h3>
    <input type="text" id="walletNumber" placeholder="เบอร์ Wallet">
    <button onclick="updateWallet()" class="btn-animate">บันทึก</button>

    <h3>เปลี่ยนสีเว็บ</h3>
    <input type="color" id="bgColor">
    <button onclick="changeColor()" class="btn-animate">เปลี่ยนสี</button>
</div>

<!-- Pop-up สำหรับการสั่งซื้อ -->
<div id="orderPopup" class="popup">
    <span class="close-popup" onclick="closePopup()">&times;</span>
    <h2>ข้อมูลการชำระเงิน</h2>
    <p>กรุณาชำระเงินที่หมายเลข Wallet:</p>
    <p id="walletInfo">063-716-6416</p>

    <!-- เพิ่มช่องกรอก QR -->
    <label for="qrCode">กรุณาสแกน QR:</label>
    <input type="text" id="qrCode" placeholder="กรอก QR Code" style="width: 100%; padding: 10px; margin-top: 10px;">
    
    <button onclick="closePopup()" class="confirm-btn">ปิด</button>
</div>

<script>
    function toggleAdminPanel() {
        let panel = document.getElementById("adminPanel");
        panel.style.display = (panel.style.display === "block") ? "none" : "block";
    }

    function addProduct() {
        let productName = document.getElementById("productName").value;
        let productPrice = document.getElementById("productPrice").value;
        if (!productName || !productPrice) return alert("กรุณาใส่ชื่อและราคาสินค้า");

        let products = JSON.parse(localStorage.getItem("products")) || [];
        products.push({ name: productName, price: productPrice });
        localStorage.setItem("products", JSON.stringify(products));
        loadProducts();
    }

    function updateWallet() {
        let walletNumber = document.getElementById("walletNumber").value;
        if (!walletNumber) return alert("กรุณากรอกเบอร์ Wallet");
        localStorage.setItem("wallet", walletNumber);
        alert("เบอร์ Wallet ได้รับการบันทึกแล้ว");
    }

    function changeColor() {
        let newColor = document.getElementById("bgColor").value;
        document.body.style.backgroundColor = newColor;
        localStorage.setItem("bgColor", newColor);
        alert("สีเว็บได้เปลี่ยนเรียบร้อยแล้ว");
    }

    function loadProducts() {
        let productList = document.getElementById("productList");
        let adminProductList = document.getElementById("adminProductList");
        productList.innerHTML = "";
        adminProductList.innerHTML = "";
        let products = JSON.parse(localStorage.getItem("products")) || [];

        products.forEach((product, index) => {
            let item = document.createElement("p");
            item.className = "product-item";
            item.textContent = `${product.name} - ${product.price} บาท`;
            productList.appendChild(item);

            let adminItem = document.createElement("li");
            adminItem.innerHTML = `${product.name} - <input type="number" class="price-input" value="${product.price}" onchange="updatePrice(${index}, this.value)"> บาท `;
            
            let delBtn = document.createElement("button");
            delBtn.textContent = "ลบ";
            delBtn.className = "delete-btn btn-animate";
            delBtn.onclick = function() {
                products.splice(index, 1);
                localStorage.setItem("products", JSON.stringify(products));
                loadProducts();
            };
            adminItem.appendChild(delBtn);
            adminProductList.appendChild(adminItem);
        });
    }

    function updatePrice(index, newPrice) {
        let products = JSON.parse(localStorage.getItem("products"));
        products[index].price = newPrice;
        localStorage.setItem("products", JSON.stringify(products));
        loadProducts();
    }

    function showPaymentInfo() {
        let wallet = localStorage.getItem("wallet") || "063-716-6416";
        document.getElementById("walletInfo").textContent = wallet;
        document.getElementById("orderPopup").style.display = "block";
    }

    function closePopup() {
        document.getElementById("orderPopup").style.display = "none";
    }

    document.addEventListener("DOMContentLoaded", () => {
        let savedColor = localStorage.getItem("bgColor");
        if (savedColor) document.body.style.backgroundColor = savedColor;
        loadProducts();
    });
</script>

</body>
</html>on 
