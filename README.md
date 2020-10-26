# การติดตั้ง
1. โคลนโปรเจ็ค
    ```powershell
    git clone https://github.com/tungman08/yii-oidc.git
    ```
2. ติดตั้ง yii package
    ```powershell
    composer install
    ```
3. ไฟล์แก้ไข
    * /composer.json เพิ่ม package เพื่อใช้ openid connection ดังนี้
        ```
        "require": {
            "web-token/jwt-checker": ">=1.0 <3.0",
            "web-token/jwt-key-mgmt": ">=1.0  <3.0",
            "web-token/jwt-signature": "~1.0 <3.0",
            "web-token/jwt-signature-algorithm-hmac": "~1.0  <3.0",
            "web-token/jwt-signature-algorithm-ecdsa": "~1.0  <3.0",
            "web-token/jwt-signature-algorithm-rsa": "~1.0  <3.0",
            "yiisoft/yii2-authclient": "~2.2.7",
        },
        ```
    * /config/web.php เพิ่มโค้ดดังนี้
        ```
        'components' => [
            'authClientCollection' => [
                'class' => 'yii\authclient\Collection',
                'clients' => [
                    'oidc' => [
                            'class' => 'yii\authclient\OpenIdConnect',
                            'issuerUrl' => 'http://localhost:8080/auth/realms/master',
                            'clientId' => 'yii-oidc',
                            'clientSecret' => '93d4915a-be3e-4eea-ac34-f8209bf12154',
                            'name' => 'Keycloak',
                            'title' => 'Keycloak OpenID Connect',
                    ],
                ],
            ],
            // ...
        ]
        ```
    * /controllers/SiteController.php เพิ่มโค้ดดังนี้
        ```
        /**
        * {@inheritdoc}
        */
        public function actions()
        {
            return [
                // ...
                'auth' => [
                    'class' => 'yii\authclient\AuthAction',
                    'successCallback' => [$this, 'oAuthSuccess'],
                ],
            ];
        }

        // แสดงข้อมูลหลังจาก authen
        public function oAuthSuccess($client) {
            // get user data from client
            $userAttributes = $client->getUserAttributes();
            echo '<pre>';
            var_dump($userAttributes);
            echo '</pre>';
            die();
        }
        ```
    * /views/site/login.php เพิ่มโค้ดดังนี้
        ```
        <!-- external authen -->
        <?php $authAuthChoice = AuthChoice::begin(['baseAuthUrl' => ['site/auth']]); ?>

            <?php foreach ($authAuthChoice->getClients() as $client): ?>
                <?php echo $authAuthChoice->clientLink($client, 'Login with '.ucfirst($client->getName()), ['class' => 'btn btn-primary btn-block']) ?>
            <?php endforeach; ?>

        <?php AuthChoice::end(); ?>
        ```
