![](BHP_website.PNG)

In this data science project, I built a real estate price prediction website step-by-step. Initially, I developed a predictive model utilizing sklearn and linear regression based on a dataset of Bangalore home prices from Kaggle. Subsequently, I wrote a Python Flask server to manage HTTP requests using the model to provide price predictions. The user interface was crafted using HTML, CSS, and JavaScript, allowing users to input details such as home area and bedroom count to obtain price estimates. The project incorporated various data science methodologies, including data loading, cleaning, outlier removal, feature engineering, dimensionality reduction, hyperparameter tuning with GridSearchCV, and k-fold cross-validation. Tools used included Python, Numpy, Pandas, Matplotlib, Jupyter Notebook, Visual Studio Code, and PyCharm.

Deployment steps involved setting up an AWS EC2 instance, configuring security groups for HTTP traffic, and installing and setting up Nginx to serve the application and route API calls. Files were transferred to the EC2 instance using WinSCP. Configuration files for Nginx were created and symlinked to direct traffic appropriately. The Flask server was initiated after installing necessary Python packages, making the website accessible and functional online at my specified cloud URL.

# Deploy this app to cloud (AWS EC2)

1. Create EC2 instance using amazon console, also in security group add a rule to allow HTTP incoming traffic
2. Now connect to your instance using a command like this,
```
ssh -i "C:\Users\Viral\.ssh\Banglore.pem" ubuntu@ec2-3-133-88-210.us-east-2.compute.amazonaws.com
```
3. nginx setup
   1. Install nginx on EC2 instance using these commands,
   ```
   sudo apt-get update
   sudo apt-get install nginx
   ```
   2. Above will install nginx as well as run it. Check status of nginx using
   ```
   sudo service nginx status
   ```
   3. Here are the commands to start/stop/restart nginx
   ```
   sudo service nginx start
   sudo service nginx stop
   sudo service nginx restart
   ```
   4. Now when you load cloud url in browser you will see a message saying "welcome to nginx" This means your nginx is setup and running.
4. Now you need to copy all your code to EC2 instance. You can do this either using git or copy files using winscp. I used winscp. You can download winscp from here: https://winscp.net/eng/download.php
5. Once you connect to EC2 instance from winscp (instruction in a youtube video), you can now copy all code files into /home/ubuntu/ folder. The full path of your root folder is now: **/home/ec2-user/House_Price_Prediction_Bengaluru**
6.  After copying code on EC2 server now we can point nginx to load our property website by default. For below steps,
    1. Create this file /etc/nginx/sites-available/bhp.conf. The file content looks like this,
    ```
    server {
	    listen 80;
            server_name bhp;
            root /home/ec2-user/House_Price_Prediction_Bengaluru/client;
            index app.html;
            location /api/ {
                 rewrite ^/api(.*) $1 break;
                 proxy_pass http://127.0.0.1:5000;
            }
    }
    ```
    2. Create symlink for this file in /etc/nginx/sites-enabled by running this command,
    ```
    sudo ln -v -s /etc/nginx/sites-available/bhp.conf
    ```
    3. Remove symlink for default file in /etc/nginx/sites-enabled directory,
    ```
    sudo unlink default
    ```
    4. Restart nginx,
    ```
    sudo service nginx restart
    ```
7. Now install python packages and start flask server
```
sudo apt-get install python3-pip
sudo pip3 install -r /home/ubuntu/BangloreHomePrices/server/requirements.txt
python3 /home/ubuntu/BangloreHomePrices/client/server.py
```
Running last command above will prompt that server is running on port 5000.
8. Now just load your cloud url in browser (for me it was http://ec2-3-133-88-210.us-east-2.compute.amazonaws.com/) and this will be fully functional website running in production cloud environment



