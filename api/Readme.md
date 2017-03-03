#S3 Protal APIs 

## 系統環境

| OS           | Host Name    | IP Address |
| :----------: |:------------:| :---------:|
| Ubuntu 14.04 | inwin-s3-api | 10.26.1.119|

## 建置 Laravel 

首先，更新並安裝 Apache 、 MySQL 與 PHP 相關套件

```
sudo apt-get update
sudo apt-get -y install apache2 mysql-server php5-mysql php5 libapache2-mod-php5 php5-mcrypt php5-curl php5-gd php5-intl php5-xsl php5-ldap php5-ssh2
```

接下來，使用編輯器將 Host Name 如下填入 `/etc/hosts/`

```
127.0.0.1 localhost
127.0.0.1 inwin-s3-api

# The following lines are desirable for IPv6 capable hosts
...
```

接著，將以下指令填入 `/etc/apache2/apache2.conf` 的最後面

```
...
ServerName inwin-s3-api
```
> 切記，ServerName 依照使用者命名需自行調整

接下來，安裝 git 套件

```
sudo apt-get -y install git
```

安裝完 git 後，將 s3-protal-api project 下載至本機 `/var/www/html` 目錄

```
sudo git clone https://github.com/inwinstack/s3-portal-api.git
```
> 注意，記得使用 `git checkout` 切換至最新的 branch

下載完畢後，修改 project 權限

```
sudo chmod 777 -R s3-portal-api
```

接著，進入 `s3-portal-api` 目錄並下載及安裝 composer

```
sudo curl -sS https://getcomposer.org/installer | sudo php -- --install-dir=/usr/local/bin --filename=composer
```
> 安裝完畢後，可輸入 `composer` 指令檢查是否有安裝成功

接下來，將原本的 `.env.example` 複製並命名為 `.env`

```
sudo cp .env.example .env
```

複製完成後，使用編輯器打開 `.env` 並且修改以下幾個部分

```
...
DB_HOST=localhost       # 輸入 MySQL 所在位址 
DB_DATABASE=homestead   # 輸入 Database Name
DB_USERNAME=homestead   # 輸入 User Name
DB_PASSWORD=secret      # 輸入 User Password

...

AccessKey=acceAccessKeysskey       # 輸入 acceAccessKeysskey
SecretKey=SecretKey                # 輸入 SecretKey
Region=defautl
ServerURL=S3_HTTP_URL              # 輸入 S3 URL
AdminEntryPoint=AdminEntryPoint    # 輸入 AdminEntryPoint
```

編輯完畢並儲存，接著安裝相依套件

```
composer install
```

接下來，修改 `/etc/apache2/sites-available/000-default.conf`

```
將

DocumentRoot /var/www/html

改成

DocumentRoot /var/www/html/s3-portal-api/public

<Directory /var/www/html/s3-portal-api/public >
  RewriteEngine On
  AllowOverride All
</Directory>
```

修改完後，使用下列指令啟動 `rewrite module`

```
sudo a2enmod rewrite
```

最後重新啟動 apache2

```
sudo service apache2 restart
```

## 初始化 MySQL

僅需在 s3 project 目錄下執行下列指令即可

```
php artisan migrate
```

> 注意，該指令會參考 `.env` 內所設定的 database ，在執行指令之前必須手動至 MySQL 內建立相同名稱的資料庫，且該資料庫需先手動建立完畢！

最後進入 `s3-portal-api` 目錄建立 Root 帳號

```
php artisan db:seed
```

注意：預設帳號(root@inwinstack.com)，密碼(password)
