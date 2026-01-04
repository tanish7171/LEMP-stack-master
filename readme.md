# Using LEMP stack - Registration Form Deployment  Project
### This Project hepls how to deploy an simple Dynamic Registration Form using ***LEMP stack***  ( **Linux** , **Nginx** , **MySQL** , **PHP** ) on  a Cloud based Linux Server ***EC2***. In this project we deploying a ***Registration Form*** on a Single server, in that we use NGINX server , MYSQL database , PHP Server side scripting language which connected to each other.

### Project overview
**LEMP**  Stands for :
- **L** - Linux : Operating system 
- **E** - Nginx : High performance web server 
- **M** - MySQL : Database server to database management
- **P** - PHP   : Server side scripting language,to process dynamic content
# LEMP Stack diagram
![alt text](<image/Image Jul 26, 2025, 11_38_22 PM.png>)
This Project Shows :
1. How to create a Nginx server 
2. How to create PHP server
3. How to create Database
4. How to connect database to main server
5. How to Deploy Registration Form 
6. After filling form how or where data is Store 
  , so lets see step by step.

## 🛠️Steps to do :
### Step.1) Create Security Group
- Go to AWS console on the search bar search ***EC2*** ,click on EC2
- In the EC2 console .on the let side panel their is `Network & Security` click on them
- In that click on `Security Groups`, 
- In that click on `create security group`
- do some basic configuration
```
 > Security group name : LEMP-server 
 > Description : allow ssh,https,http
 > VPC : (default vpc )
```
- In ***inbound rules*** add the inbound rules ,click on Add rule
```
Type : SSH , Source : 0.0.0.0/0 (anywhere)
Type : HTTP , Source : 0.0.0.0/0 (anywhere)
Type : HTTPS , Source : 0.0.0.0/0 (anywhere)
```
- In Outbound rule kept as default ,no change and click on Create.
![alt text](<image/Screenshot 2025-07-07 200059.png>)

### Step.2) Create Instance
- On Left panel of EC2 console , click on ` Instances` 
- In that instances click on `Launch instance`
- After clicking fill it
```
-> Name : LEMP-server (name the intance)
-> Application and OS Images : Amazon Linux 2023 kernel-6.1 ( just check that its is In FREE TIER ,or other mistakely click)
-> Instance type : t2.micro ( FREE TIER )
-> Key pair : .pem key ( create a key in (.pem)and add)
-> In Network setting , select existing security group : LEMP-server(existing or create new one)
-> other setting kept default
-> Click on Launch Instance
```
![alt text](<image/Screenshot 2025-07-07 200254.png>)
### Step.3) Connect to EC2 with SSH
- After creating instance , select the instance which we created and then click on `Connect` ,
- In connect page click on `SSH client` and copy the last line command
 ```
ssh -i "linux-server-key.pem"ec2-user@ec2-44-201-171-84.compute-1.amazonaws.com
                       OR
ssh -i linux-server-key.pem ec2-user@44.201.171.84 
```
- go to git bash open it and paste the above command
![alt text](<image/Screenshot 2025-07-07 200401.png>)
- For changing **Hostname** from ip to LEMP-server ,add command
```bash
sudo hostnamectl hostname LEMP-server

           #then
exit
           #then repeat the ssh step to connect 
```
![alt text](<image/Screenshot 2025-07-07 200729.png>)
### Step.4) Install Nginx , MySQL , PHP
#### Before that UPDATE the OS
```bash
sudo yum update
```
#### Install NGINX 
```bash
sudo yum install nginx -y
sudo systemctl nginx httpd
sudo systemctl nginx httpd
```
- To check that it is successfully install, hit the public ip or try this ``` curl localhost  ``` command
![alt text](<image/Screenshot 2025-07-07 201218.png>)
#### Install MySQL
```bash
sudo yum search mysql           #see where the server is written ( mariadb105-server)

sudo yum install mariadb105-server -y
sudo systemctl start mariadb
sudo systemctl enable mariadb

mariadb --version               # to check the version
```
#### Install PHP
```bash
sudo yum istall php -y
sudo systemctl start php-fpm
sudo systemctl enable php-fpm


php --version                     # to check the version php
```
### Step.5) Deploy Registration Form  
#### Change the directory,where application files are save or deployed that, ***html/***
```bash 
 cd /usr/share/nginx/html/
```
- In that directory create a signup page of HTML and submit page of PHP
```bash 
sudo vim signup.html        # to create a file signup.html and go inside the file,to write code inside the signup.html using vim editor
```
```bash
#after going inside the vim editor press ( i ) and write given html code,

<!DOCTYPE html>
<html>
<head>
<title>Signup Form</title>
</head>
<body>
<h2>Signup Form</h2>
<form action="submit.php" method="post">
<label for="name">Name:</label><br>
<input type="text" id="name" name="name" required><br><br>
 
        <label for="email">Email:</label><br>
<input type="email" id="email" name="email" required><br><br>
 
        <label for="website">Website:</label><br>
<input type="url" id="website" name="website"><br><br>
 
        <label for="comment">Comment:</label><br>
<textarea id="comment" name="comment" rows="4" cols="50"></textarea><br><br>
 
        <label>Gender:</label><br>
<input type="radio" id="female" name="gender" value="female" required>
<label for="female">Female</label><br>
 
        <input type="radio" id="male" name="gender" value="male">
<label for="male">Male</label><br>
 
        <input type="radio" id="other" name="gender" value="other">
<label for="other">Other</label><br><br>
 
        <input type="submit" value="Submit">
</form>
</body>
</html>

#and then press ( esc ) button
# and wirte ( :wq ) to save and exit
```
#### After that, restart the nginx
```bash
sudo systemctl restart nginx 
```
#### and check that its is working, by hiting Public-IP of server on Browser
![alt text](<image/Screenshot 2025-07-07 202943.png>)
- But after submiting form we get an ERROR 
![](<image/Screenshot 2025-07-07 203009.png>)
- This error due to we not connect PHP to Submit.html for that Create file (submit.php)
#### Create file ***submit.php***
```bash
sudo vim submit.php        # to create a file submit.php and go inside the file,to write code inside the submit.php using vim editor
```
```bash
#after going inside the vim editor press ( i ) and write given html code,

<?php
error_reporting(E_ALL);
ini_set('display_errors', 1);
 
$name = $_POST['name'];
$email = $_POST['email'];
$website = $_POST['website'];
$comment = $_POST['comment'];
$gender = $_POST['gender'];
 
// Database connection
$servername = "localhost";
$username = "root";
$password = "root";
$dbname = "formdb";
 
// Create connection
$conn = mysqli_connect($servername, $username, $password, $dbname);
 
// Check connection
if (!$conn) {
    die("Connection failed: " . mysqli_connect_error());
}
 
// Insert query
$sql = "INSERT INTO users (name, email, website, comment, gender)
        VALUES ('$name', '$email', '$website', '$comment', '$gender')";
?>
 
<!DOCTYPE html>
<html>
<head>
<title>Form Submission Result</title>
</head>
<body>
<?php
    if (mysqli_query($conn, $sql)) {
        echo "<h2>✅ New record created successfully!</h2>";
        echo "<h3>Submitted Information:</h3>";
        echo "<ul>";
        echo "<li><strong>Name:</strong> " . htmlspecialchars($name) . "</li>";
        echo "<li><strong>Email:</strong> " . htmlspecialchars($email) . "</li>";
        echo "<li><strong>Website:</strong> " . htmlspecialchars($website) . "</li>";
        echo "<li><strong>Comment:</strong> " . htmlspecialchars($comment) . "</li>";
        echo "<li><strong>Gender:</strong> " . htmlspecialchars($gender) . "</li>";
        echo "</ul>";
    } else {
        echo "<h3>❌ Error: " . $sql . "<br>" . mysqli_error($conn) . "</h3>";
    }
 
    mysqli_close($conn);
    ?>
</body>
</html>

#and then press ( esc ) button
# and wirte ( :wq ) to save and exit
```
#### After that, restart the php-fpm
```bash
sudo systemctl restart php-fpm 
```
#### After that , again fill the form and check 
![alt text](<image/Screenshot 2025-07-07 203205.png>)
- This error due to database not Connected, create database
### Step.6) Create Database
- To create database , go inside MYSQL
 ```bash
sudo mysql -u root -p
 ```
 - To set password to mysql ,write command
 ```bash
alter user root@localhost identified by "root";
exit;

 ```
 - again hit command 
 ```bash
sudo mysql -u root -p
password : root     # it seen to us just type root
 ```
- create database  formdb,
 ```bash
show databases;
create database formdb;
use formdb;
 ```
![alt text](<image/Screenshot 2025-07-07 203740.png>)
- create user table in formdb database,
```bash
CREATE TABLE users (
 id INT PRIMARY KEY AUTO_INCREMENT,
 name VARCHAR(20),
 email VARCHAR(100),
 website VARCHAR(255),
 gender VARCHAR(6),
 comment VARCHAR(100)
 );
```
![alt text](<image/Screenshot 2025-07-07 204011.png>)

### Step.7) Install CONNECTER to connect database to registration form
```bash
sudo yum install php8.4-mysqlnd.x86_64 -y            # to install connecter
```
- Restart all the services
```bash
sudo systemctl restart nginx
sudo systemctl restart mariadb
sudo systemctl restart php-fpm
```
#### Please check the _database connection_ in ***submit.php*** , to ensure that the ***servername , username , database name , password*** are written correctly
![alt text](<image/Screenshot 2025-07-07 204603.png>)
- If it not written correctly the error occurs like this
![alt text](<image/Screenshot 2025-07-07 204327.png>)

- All checkups done then, Hit the **Public IP** to Browser , And then add ***signup.html*** with Public IP `44.201.171.84/signup.html` and Fill the form , After Successfully submit the form. Then Check the DATA which is **Stored** in our ***DATABASE*** ,( _formdb_ ) in ( _users_ ) table
![alt text](<image/Screenshot 2025-07-07 211709.png>)

### 🏁Final Out put of our Poject :

<video controls src="image/Screen Recording 2025-07-07 211504.mp4" title="Title"></video>

## 📄Summary
So finally we Deployed a REGISTRATION FORM on a LEMP stack that is ( LINUX , NGINX , MYSQL , PHP ) hosted on an Amazon EC2 instance.It showcases a basic, secure, and scalable method to serve dynamic content and manage a backend database on a cloud platform. The LEMP stack is one of the most popular and proven open-source stacks for hosting dynamic web applications.</br>
A LEMP stack project involves deploying a dynamic web application using a combination of Linux  as the operating system, Nginx  as the high-performance web server, MySQL/MariaDB  for database management, and PHP  to process dynamic content. In this setup, Nginx handles incoming HTTP requests and serves static files or passes dynamic requests to PHP via PHP-FPM, which interacts with the MySQL database to store and retrieve data. This stack is widely used for hosting modern websites and web apps due to its efficiency, scalability, and ease of configuration, making it a popular choice for developers looking to build secure, fast, and cost-effective web solutions.