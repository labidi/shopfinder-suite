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
