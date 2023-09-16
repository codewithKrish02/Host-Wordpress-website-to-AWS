# Host Wordpress website to AWS

We are going to pick a virtual computer from the AWS and install Apache which is the webserver and we also need a database(My SQL). 

Once we are ready with the configuration of the server and database we will install the Wordpress. This wordpress does not involve in any coding, we can just create and modify our website and deploy that in to AWS.

After we deploy the webiste to AWS it will be giving us a random domain, but we will be creating a custom domain for your self and also attaching an ssl certificate to call the website to http<b>s</b> (secured).  

---
### Creating an EC2 instance:

Open your AWS Console and head to EC2 instance to create a new instance with name `Server_wordpress`. I will be chosing a Ubuntu Operating and dont change any default configurations related to the Instance type. Its fine to use the `t2.micro`.

Now, we will create a key-pair, This is used whenever you are trying to login to the instance via ssh. Give a name for you key-pair `Server_keypair` and the type of keypair to be `RSA` and the format of the private key file to be `.pem`. Once you create your key pair it will download your .pem file.

> Note: Make sure not to share your private key with anyone. Once if it gets compromised, they will be getting access to your account.

We also need to configure you Network settings. Here you need to enable the following things:
- Allow HTTPS traffic from the internet
- Allow HTTP traffic from the internet

Now you are good to go for lauching your instance.

---
### Creating an Elastic IP address:
I must acquire an Elastic IP address for my EC2 instance, as the instance generates a different IP address each time it is restarted. This consistency is essential for DNS recognition of my website.

Here are the steps to create an Elastic IP address:

1. Navigate to the EC2 Dashboard and locate the section labeled "Elastic IPs." Click on this section to access the Elastic IP creation page.

2. Once on the Elastic IP page, proceed to allocate a new Elastic IP address by clicking the appropriate button. You can leave the default configuration settings unchanged and simply click on "Allocate."

3. After successfully allocating the Elastic IP, you will have a new static IP address. The next step is to associate it with your EC2 instance. Select the newly allocated IP, go to the "Actions" menu, and click on "Associate Elastic IP address."

4. In the association process, you'll need to select your instance, named "Server_wordpress," and confirm the association.

By following these steps, you will have obtained a freshly created static IP address that can be used for connecting to your instance via SSH.

--- 
### Login to EC2 instance via SSH:

For Mac users, utilizing the built-in Terminal is an option, whereas Windows users require external software like Putty, MTputty, or MobaXterm. Below is the procedure tailored for Mac users:

To get started, right-click on the instance and select "Connect." Then, navigate to the SSH Client tab and follow these steps:

1. Launch the SSH Client.

2. Locate your private key file, which in this case is the key used to launch the instance, known as `Server_keypair.pem`.

3. To ensure proper access to the file and make sure the key isn't publicly visible, execute the following commands:
   - chmod 400 `Server_keypair.pem`
   - chmod 600 `Server_keypair.pem`

4. Now, establish a connection with your instance using the following command:
   - ssh -i server_keypair.pem 'username@public_dns' (The necessary public DNS information will be provided on the same page).

For Windows users employing MobaXterm, configuration is straightforward. You can simply attach your private key and input all the required details such as the username and host address, which is the IP address, to gain access and log in.

---
### Installing Apache Server to the Instance:
To set up an Apache web server on this instance, run the command below:
> sudo apt install apache2

To confirm whether the server is successfully installed or not, you can open your EC2 instance in a web browser using the following link:

>http://yourelasticip

Clicking on the provided link will take you to your instance and display the Apache Default page if the installation was successful.

To enable PHP on your server and establish a connection to MySQL, you'll need to install PHP and the PHP MySQL connector. Use the following command:
     
> sudo apt install php libapache2-mod-php php-mysql
     
To set up the MySQL database management system, which is essential for storing data for various applications, including WordPress, run the following command:
    
> sudo apt install mysql-server

To enhance security, it's recommended to change the MySQL authentication plugin for the root user to MySQL native password. This command allows you to access the MySQL command line:
>sudo mysql -u root
     
Then, execute the following command to make the change:
     
>ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password by 'your_strong_password';

Replace `'your_strong_password'` with the desired strong password.

As WordPress requires its own user to interact with the database, create a new user with the following command:
     
> CREATE USER 'wp_user'@localhost IDENTIFIED BY 'your_strong_password';
     
Replace `'your_strong_password'` with a secure password.

WordPress needs a separate database to store its data. Create this database using the following command:

> CREATE DATABASE wp;

To allow the newly created 'wp_user' to manage the 'wp' database, grant all privileges with this command:

> GRANT ALL PRIVILEGES ON wp.* TO 'wp_user'@localhost;
     
To exit the MySQL command line after making the necessary changes and configurations, simply type:
     
>exit;
     

These steps collectively prepare your server for hosting WordPress by installing necessary software components and configuring MySQL for secure and efficient operation.

---
### Install Wordpress: