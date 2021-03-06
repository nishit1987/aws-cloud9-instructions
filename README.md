# Getting started with Cloud9 on Amazon Web Services (AWS)

- Sign up [here](https://portal.aws.amazon.com/billing/signup#/start)
- Select **Personal** for account type
- AWS requires a valid phone number for verification
- Your credit/debit card will also be charged $1 for verification purposes, the amount will be refunded after being processed
	- See [here](https://aws.amazon.com/premiumsupport/knowledge-center/aws-authorization-charges/) for more information about the charge
- Select the *Free* **Basic Plan**
	- This plan is free for 12 months with certain usage restrictions, set a date in your calender to cancel your plan if you don't want to be charged after one year
	- See more details about the free plan [here](https://aws.amazon.com/free/?sc_channel=em&sc_campaign=wlcm&sc_publisher=aws&sc_medium=em_wlcm_1d&sc_detail=wlcm_1d&sc_content=other&sc_country=global&sc_geo=global&sc_category=mult&ref_=pe_1679150_261538020)
- Sign in to the AWS console with your new account
	- It can take up to 24 hours for your account to be verified, check your email for notification
- Once signed in select the **AWS services** search bar and type `IAM` then select **IAM Manage User Access and Encryption Keys**
- Select **Users** > **Add User**
- name the user **admin**
- Create a custom password
- Uncheck option to have user be prompted to reset password on next sign in
- Click **Next: Permissions**
- Click **Create group**
- Name it **wdb** and check **AdministratorAccess** from the top of the list
- Click **Create group** then scroll down and click **Next: Review**
- Click **Create user**
- Click the url that comes after **"Users with AWS Management Console access can sign-in at:"** and bookmark it as **c9 IAM signin**
- Sign in with your IAM username and password
- Type **cloud9** into the **AWS services** search bar and select **Cloud9 A Cloud IDE for Writing, Running, and Debugging Code**
- If your account has been verified then you will be able to select **Create environment**
- Name is **wdb** and click **Next step**
- Leave default settings and click **Next step** again
- Scroll down and click **Create environment**

## Check node and npm

- Once inside your c9 environment (previously called a workspace) type `node -v` into the terminal, you should see v6.11.4 (current version being used at the time of the making of this tutorial)
- Now type `npm -v`, you should see 3.10.10 (or higher)

## MongoDB Instructions
*(skip this section and see below for MySQL Instructions if coming from MySQL course)*

- Enter `touch mongodb-org-3.6.repo` into the terminal
- Now open the **mongodb-org-3.6.repo** file in your code editor (select it from the left-hand file menu) and paste the following into it then save the file:

```
[mongodb-org-3.6]
name=MongoDB Repository
baseurl=https://repo.mongodb.org/yum/amazon/2013.03/mongodb-org/3.6/x86_64/
gpgcheck=1
enabled=1
gpgkey=https://www.mongodb.org/static/pgp/server-3.6.asc
```

- Now run the following in your terminal:



```
sudo mv mongodb-org-3.6.repo /etc/yum.repos.d
sudo yum install -y mongodb-org
```
- Close the **mongodb-org-3.6.repo** file and press **Close tab** when prompted
- Now start the mongo daemon with: `sudo service mongod start`
	- This will replace the `./mongod` command from before
- The terminal will return `Starting mongod: [  OK  ]`
- Now open the shell with: `mongo`
- When done working exit with `ctrl + d` and stop the service by entering the following into the terminal: `sudo service mongod stop`
- Test your install by creating a file in **~/environment** named **cats.js** and pasting the following code into the file:

```
var mongoose = require("mongoose");
mongoose.connect("mongodb://localhost/cat_app", {useMongoClient: true});

var catSchema = new mongoose.Schema({
   name: String,
   age: Number,
   temperament: String
});

var Cat = mongoose.model("Cat", catSchema);

Cat.create({
   name: "Snow White",
   age: 15,
   temperament: "Bland"
}, function(err, cat){
    if(err){
        console.log(err);
    } else {
        console.log(cat);
    }
});
```

- Save the file, install mongoose with `npm i mongoose` then run it from the terminal with `node cats.js`
- You should get the following output:

```
{ __v: 0,
  name: 'Snow White',
  age: 15,
  temperament: 'Bland',
  _id: 5a2e9d6ce6352614fd77c181 }
```
- Now use `ctrl + c` to exit node and get back to your bash terminal
- If you want to disable journaling to save disk space then create a file named **mongod.conf** in ~/environment and paste the following code into it:

```
# mongod.conf

# for documentation of all options, see:
#   http://docs.mongodb.org/manual/reference/configuration-options/

# where to write logging data.
systemLog:
  destination: file
  logAppend: true
  path: /var/log/mongodb/mongod.log

# Where and how to store data.
storage:
  dbPath: /var/lib/mongo
  journal:
    enabled: false
#  engine:
#  mmapv1:
#  wiredTiger:

# how the process runs
processManagement:
  fork: true  # fork and run in background
  pidFilePath: /var/run/mongodb/mongod.pid  # location of pidfile
  timeZoneInfo: /usr/share/zoneinfo

# network interfaces
net:
  port: 27017
  bindIp: 127.0.0.1  # Listen to local interface only, comment to listen on all interfaces.


#security:

#operationProfiling:

#replication:

#sharding:

## Enterprise-Only Options

#auditLog:

#snmp:
```

- Now save the file and enter `sudo mv mongod.conf /etc/mongod.conf` in the terminal

## MySQL Instructions

- Enter `sudo service mysqld start` into the terminal
- Enter `/usr/libexec/mysql55/mysql_secure_installation` and follow the steps for setting up your root account and password.
	- When prompted for the initial password press enter
		- If this step fails then enter `/usr/libexec/mysql55/mysqladmin -u root password 'new-password'`
			- Be sure to replace `'new-password'` with your password
		- Now start the secure installation over again with `/usr/libexec/mysql55/mysql_secure_installation` and enter the password you just created
	- Disable remote root access and remove test database and anonymous user during the secure installation steps

- Start the mysql shell with root user access by entering: `mysql -uroot -p` and typing in your root password when prompted
	- Password will be hidden while typing it in, press enter when done typing and the shell will start if the password is correct
- Once inside of the shell test it out with the following commands:



```
CREATE database test;
USE test;
CREATE TABLE pet (name VARCHAR(20), owner VARCHAR(20), species VARCHAR(20));
INSERT INTO pet (name, owner, species) VALUES('Loki', 'Ian', 'Dog');                                                                                                                                                    
SELECT * FROM pet;
```

**Result:**

```
+------+-------+---------+
| name | owner | species |
+------+-------+---------+
| Loki | Ian   | Dog     |
+------+-------+---------+
```
- When you're done working you can exit the shell by typing `exit` or pressing `ctrl + c`
- Stop the mysql daemon with `sudo service mysqld stop`
