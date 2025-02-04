# Shopfinder-suite
## Introduction

ShopFinder suite is a metapackage contains 4 different modules :
- Shopfinder module : Define service contracts and all necessary model classes to persist and read data from DB.
- Shopfinder-admin-ui : Add creating and editing shops from the Admin area.
- Shopfinder-graph-ql : Integrate GraphQL endpoints in order to consume Shop data.
- Shopfinder-rest : Integrate REST endpoints in order to consume Shop data.

## Magento Instalation 

#### Create your project directory then go into it:
mkdir -p ~/workspace/magento
cd $_

#### Run this automated one-liner from the directory you want to install your project.
curl -s https://raw.githubusercontent.com/markshust/docker-magento/master/lib/onelinesetup | bash -s -- magento2.local community 2.4.7-p3

## Modules Instalation   

#### First we need to add the repositories urls to the composer.json file
```Json
"repositories": {
    .....,
    "saddemlabidi/module-shopfinder": {
            "type": "git",
            "url": "https://github.com/labidi/shopfinder.git"
        },
        "saddemlabidi/module-shopfinder-admin-ui": {
            "type": "git",
            "url": "https://github.com/labidi/shopfinder-admin-ui.git"
        },
        "saddemlabidi/module-shopfinder-graph-ql": {
            "type": "git",
            "url": "https://github.com/labidi/shopfinder-graph-ql.git"
        },
        "saddemlabidi/module-shopfinder-rest": {
            "type": "git",
            "url": "https://github.com/labidi/shopfinder-rest.git"
        },
        "saddemlabidi/shopfinder-suite": {
            "type": "git",
            "url": "https://github.com/labidi/shopfinder-suite.git"
        }
}
```

#### Run installation commande via composer 
```bach
bin/composer require saddemlabidi/shopfinder-suite
```
#### After installation we should have all module suites installed in our vendor folder

![image](https://github.com/user-attachments/assets/35018306-c6eb-426b-9676-519155ec987d)

#### Make sure to enable all modules and run setup upgrade and code compilation : 

```bash
./bin/magento module enable Saddemlabidi_Shopfinder Saddemlabidi_ShopfinderAdminUi Saddemlabidi_ShopfinderGraphQl Saddemlabidi_ShopfinderRest
./bin/magento setup:upgrade
./bin/magento setup:di:compile

```

#### Navigate to the admin area , you should be able to to see a new menu item in the content section area : 

![image](https://github.com/user-attachments/assets/eab1d4bc-6170-45c4-a88d-32cf7dde036c)


## Unit/api-functional tests

In order to do so create we need to navigate to src/dev/tests/api-functional/testsuite.
Create our test class under this folder structere src/dev/tests/api-functional/testsuite/Saddemlabidi/ShopfinderGraphQl/ShopGraphQlTest.php:9 






