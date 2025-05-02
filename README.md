# Razorpay Integration in Node.js E-commerce Web Application

Welcome to the Razorpay integration in a Node.js E-commerce Web Application. This project demonstrates how to implement Razorpay for online payments in a modern e-commerce platform using various technologies.Follow this README to understand the setup and features of this application.

Curious how businesses use this in production? Read how we do it at Seven Square → [[link to blog](https://www.sevensquaretech.com/razorpay-api-integration-guide-in-nodejs/)]

---

## ⚙️ Tech Stack

- **Node.js**: JavaScript runtime built on Chrome's V8 JavaScript engine.
- **Express.js**: Fast, unopinionated, minimalist web framework for Node.js.
- **Tailwind CSS**: Utility-first CSS framework for rapidly building custom user interfaces.
- **EJS (Embedded JavaScript Templates)**: Simple templating language that lets you generate HTML markup with plain JavaScript.
- **AJAX**: A set of web development techniques for asynchronous communication.
- **Razorpay**: Payment gateway for accepting online payments in India.

---

## ✨ Features

- **Modern UI**: Responsive and user-friendly design using Tailwind CSS.
- **Product Purchase**: Browse and buy products.
- **Online Payments**: Secure payments through Razorpay.

---

## 📅 Installation

1. **Clone the repository**:

   ```sh
   git clone https://github.com/yourusername/your-repo-name.git
   cd your-repo-name
   ```

2. **Install dependencies**:

   ```sh
   npm install
   ```

3. **Set up environment variables**:

   Create a `.env` file in the root directory:

   ```env
   RAZORPAY_KEY_ID=your_key_id
   RAZORPAY_KEY_SECRET=your_key_secret
   ```

4. **Start the server**:

   ```sh
   npm start
   ```

---

## 🔎 Usage

1. **Open your browser**:
   Navigate to `http://localhost:3000`

2. **Browse products**:
   View and select items to add to your cart.

3. **Proceed to checkout**:
   Complete your purchase using Razorpay.

---

## 📃 Code Overview

### 🚀 Razorpay Setup

1. **Install Razorpay package**:

   ```sh
   npm install razorpay
   ```

2. **Configure Razorpay**:

   ```javascript
   const Razorpay = require("razorpay");

   const razorpay = new Razorpay({
     key_id: process.env.RAZORPAY_KEY_ID,
     key_secret: process.env.RAZORPAY_KEY_SECRET,
   });
   ```

3. **Create Order API**:

   ```javascript
   router.post("/createOrder", (req, res) => {
     try {
       const amount = req.body.amount * 100;
       const options = {
         amount,
         currency: "INR",
         receipt: `receipt_order_${Date.now()}`,
       };
       razorpay.orders.create(options, (err, order) => {
         if (!err) {
           res.status(200).send({
             success: true,
             msg: "Order Created",
             order_id: order.id,
             amount,
             key_id: process.env.RAZORPAY_KEY_ID,
             product_name: req.body.name,
             description: req.body.description,
             contact: "your_contact_number",
             name: "your_name",
             email: "example@example.com",
           });
         } else {
           res
             .status(400)
             .send({ success: false, msg: "Something went wrong" });
         }
       });
     } catch (error) {
       res.status(500).send({ success: false, msg: "Server Error" });
     }
   });
   ```

4. **Verify Payment**:

   ```javascript
   app.post("/verify/payment", (req, res) => {
     const { razorpay_order_id, razorpay_payment_id, razorpay_signature } =
       req.body;
     const crypto = require("crypto");

     const shasum = crypto.createHmac(
       "sha256",
       process.env.RAZORPAY_KEY_SECRET
     );
     shasum.update(`${razorpay_order_id}|${razorpay_payment_id}`);
     const digest = shasum.digest("hex");

     if (digest === razorpay_signature) {
       res.json({ status: "success" });
     } else {
       res.json({ status: "failure" });
     }
   });
   ```

---

## 📱 Frontend Integration

1. **Include Razorpay Script**:

   ```html
   <script src="https://checkout.razorpay.com/v1/checkout.js"></script>
   ```

2. **Trigger Checkout**:

   ```javascript
   $(document).ready(() => {
     $(".pay-form").submit(function (e) {
       e.preventDefault();
       var formData = $(this).serialize();
       $.ajax({
         url: "/createOrder",
         type: "POST",
         data: formData,
         success: function (res) {
           if (res.success) {
             var options = {
               key: res.key_id,
               amount: res.amount,
               currency: "INR",
               name: res.product_name,
               image:
                 "https://plus.unsplash.com/premium_photo-1678187782578-70b5a348f502",
               order_id: res.order_id,
               handler: function (response) {
                 alert("Payment Successful");
               },
               prefill: {
                 contact: res.contact,
                 name: res.name,
                 email: res.email,
               },
               notes: {
                 description: res.description,
               },
               theme: {
                 color: "#2300a3",
               },
             };
             var razorpayObject = new Razorpay(options);
             razorpayObject.on("payment.failed", function (response) {
               alert("Payment Failed");
             });
             razorpayObject.open();
           } else {
             alert(res.msg);
           }
         },
       });
     });
   });
   ```

   Enjoy a seamless and secure online shopping experience with our Node.js E-commerce Web Application integrated with Razorpay.
