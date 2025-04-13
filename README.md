# ?? WhatsApp Fee Reminder System

## Overview
The WhatsApp Fee Reminder System is a web application designed to automate fee reminders for students via WhatsApp. It allows educational institutions to send timely reminders to students about pending fee payments, improving fee collection efficiency.

## Features
- User Authentication: Secure login and registration using JWT.
- Student Management: Upload student data from Excel files.
- WhatsApp Integration: Send reminders directly through WhatsApp Web.
- Automated Scheduling: Reminders are sent automatically based on due dates.
- Real-Time Updates: Displays live connection status and reminder logs.
- International Number Support: Handles phone numbers with country codes like +91, +44, etc.

## Technologies Used
- **Backend**: Node.js, Express
- **Database**: MySQL
- **Authentication**: JSON Web Tokens (JWT)
- **File Uploads**: express-fileupload
- **Real-Time Communication**: Socket.IO
- **WhatsApp Automation**: whatsapp-web.js
- **Task Scheduling**: node-cron
- **Logging**: Winston
- **Frontend**: HTML, CSS, JavaScript, Bootstrap

## Installation

### Prerequisites
- Node.js (v14 or higher)
- MySQL Server
- npm (Node Package Manager)

### Steps

#### 1. Clone the Repository
```bash
git clone https://github.com/yourusername/WhatsappFeeReminder.git
cd WhatsappFeeReminder
```

#### 2. Install Dependencies
```bash
npm install
```

#### 3. Set Up Environment Variables
Create a `.env` file in the root directory and add the following:
```env
DB_HOST=localhost
DB_USER=root
DB_PASSWORD=your_password
DB_NAME=whatsapp_reminder
JWT_SECRET=your_jwt_secret
```

#### 4. Create the Database
```sql
CREATE DATABASE whatsapp_reminder;
USE whatsapp_reminder;

CREATE TABLE users (
 id VARCHAR(36) PRIMARY KEY,          
 username VARCHAR(50) UNIQUE NOT NULL,
 password VARCHAR(255) NOT NULL,
 whatsapp_session_data TEXT,
 created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE TABLE students (
 id INT AUTO_INCREMENT PRIMARY KEY,
 user_id VARCHAR(36) NULL,        
 name VARCHAR(100) NOT NULL,
 phone VARCHAR(20) NOT NULL,         
 amount DECIMAL(10,2) NOT NULL,       
 due_date DATE,                      
 last_reminder_sent DATETIME NULL,   
 created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
 message_time DATETIME NULL,
 status VARCHAR(20) NOT NULL DEFAULT 'pending',
 reminder_scheduled TINYINT(1) DEFAULT 0,
 reminder_sent TINYINT(1) DEFAULT 0,
 reminder_date DATETIME NULL,
 is_sent TINYINT(1) DEFAULT 0,      
 retry_count INT DEFAULT 0,
 last_attempt DATETIME NULL,
 FOREIGN KEY (user_id) REFERENCES users(id)
);

CREATE TABLE whatsapp_sessions (
 id INT AUTO_INCREMENT PRIMARY KEY,
 user_id VARCHAR(36) NOT NULL,       
 status VARCHAR(50) NOT NULL,
 created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
 last_active TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
 FOREIGN KEY (user_id) REFERENCES users(id)
);
```

#### 5. Start the Application

##### ??? Start Backend Server
```bash
npm start
```

##### ?? Start Frontend Server
In a separate terminal, navigate to the `build` folder:
```bash
cd build
npx http-server -p 5000 --proxy http://localhost:3000
```

Then open [http://localhost:5000/dashboard.html](http://localhost:5000/dashboard.html) in your browser.

---

## Usage

1. **Register**: Create a new account using the registration page.
2. **Login**: Sign in to access the dashboard.
3. **Upload Excel File**: Add student records with due dates and phone numbers.
4. **Reminders**: Messages are sent automatically when a due date is reached or passed.

---

## Cron Jobs

The system includes a daily cron job:
- It runs at midnight every day (`0 0 * * *`)
- Checks for students whose due date is 2 days past
- Sends a WhatsApp reminder using `whatsapp-web.js`
- Marks the reminder as sent in the database

---

## Acknowledgments

- [`whatsapp-web.js`](https://github.com/pedroslopez/whatsapp-web.js) for WhatsApp integration
- [`express`](https://expressjs.com/) for web server
- [`Socket.IO`](https://socket.io/) for real-time updates


