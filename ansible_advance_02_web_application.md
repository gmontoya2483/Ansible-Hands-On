# Ansible Advance - Web Application

[BACK TO README](README.md)

## Ansible Advanced Web Application

The objective of this chapter is to show how to deploy a Web application in a single host by using a single playbook. The idea is to show how to improve it in future section.  
This is the base for future chapters.

* **inventory.txt**

    ```txt
    #Inventory
    db_and_web_server ansible_connection=ssh ansible_ssh_pass=Passw0rd ansible_host=192.168.1.14
    ```

* **playbook.yml**

    ```yaml
    -
    name: Deploy a web application
    hosts: db_and_web_server
    vars:
        db_name: employee_db
        db_user: db_user
        db_password: Passw0rd
        
    tasks:
        - name: Install dependencies
        apt: name={{ item }} state=present
        with_items:
        - python
        - python-setuptools
        - python-dev
        - build-essential
        - python-pip
        - python-mysqldb

        - name: Install MySQL database
        apt:
            name: "{{ item }}"
            state:  present
        with_items:
        - mysql-server
        - mysql-client

        - name: Start Mysql Service
        service:
            name: mysql
            state: started
            enabled: yes

        - name: Create Application Database
        mysql_db: name={{ db_name }} state=present


        - name: Create Application DB User
        mysql_user: name={{ db_user }} password={{ db_password }} priv='*.*:ALL' host='%' state='present'

        - name: Install Python Flask dependencies
        pip:
            name: '{{ item }}'
            state: present
        with_items:
        - flask
        - flask-mysql

        - name: Copy web-server code
        copy: src=app.py dest=/opt/app.py

        - name: Start web-application
        shell: FLASK_APP=/opt/app.py nohup flask run --host=0.0.0.0 &
    ```


* **app.py**

    ```python
    # -------------------------------------------------
    #
    # This is the web application code. DO NOT MODIFY
    #
    # -------------------------------------------------

    from flask import Flask
    from flask.ext.mysql import MySQL
    app = Flask(__name__)

    mysql = MySQL()

    # MySQL configurations
    app.config['MYSQL_DATABASE_USER'] = 'db_user'
    app.config['MYSQL_DATABASE_PASSWORD'] = 'Passw0rd'
    app.config['MYSQL_DATABASE_DB'] = 'employee_db'
    app.config['MYSQL_DATABASE_HOST'] = 'localhost'
    mysql.init_app(app)

    conn = mysql.connect()

    cursor = conn.cursor()

    @app.route("/")
    def main():
        return "Welcome!"

    @app.route('/how are you')
    def hello():
        return 'I am good, how about you?'

    @app.route('/read from database')
    def read():
        cursor.execute("SELECT * FROM employees")
        row = cursor.fetchone()
        result = []
        while row is not None:
        result.append(row[0])
        row = cursor.fetchone()

        return ",".join(result)

    if __name__ == "__main__":
        app.run()
    ```