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
Create our test class under this folder structere src/dev/tests/api-functional/testsuite/Saddemlabidi/ShopfinderGraphQl/ShopGraphQlTest.php

ShopGraphQlTest class content : 

```php
<?php

declare(strict_types=1);

namespace Saddemlabidi\ShopfinderGraphQl\Test\Integration;

use Magento\TestFramework\TestCase\GraphQlAbstract;
use Magento\Framework\GraphQl\Exception\GraphQlInputException;

class ShopGraphQlTest extends GraphQlAbstract
{
    /**
     * Test fetching all shops via GraphQL.
     */
    public function testGetAllShopsGraphQl(): void
    {
        $query = <<<QUERY
{
    shops {
        id
        identifier
        name
        country
        image
        longitude_latitude
        created_at
        updated_at
    }
}
QUERY;
        $response = $this->graphQlQuery($query);
        $this->assertArrayHasKey('shops', $response, 'Expected "shops" key in response');
        $this->assertIsArray($response['shops'], 'Expected "shops" to be an array');
    }

    /**
     * Test updating a shop via a GraphQL mutation.
     *
     * This test assumes a shop with id 1 exists. Adjust the values as needed.
     */
    public function testUpdateShopGraphQl(): void
    {
        $mutation = <<<MUTATION
mutation {
  updateShop(input: {
    id: 1,
    identifier: "shop-001-updated-graphql",
    name: "Updated Shop Name GraphQL",
    country: "USA",
    image: "updated-image-graphql.jpg",
    longitude_latitude: "40.7128,-74.0060"
  }) {
    id
    identifier
    name
    country
    image
    longitude_latitude
    created_at
    updated_at
  }
}
MUTATION;
        $response = $this->graphQlMutation($mutation);
        $updatedShop = $response['updateShop'];
        $this->assertEquals("shop-001-updated-graphql", $updatedShop['identifier']);
        $this->assertEquals("Updated Shop Name GraphQL", $updatedShop['name']);
    }

    /**
     * Test fetching a single shop by its identifier via GraphQL.
     */
    public function testGetShopByIdentifierGraphQl(): void
    {
        $query = <<<QUERY
{
    shop(identifier: "shop-001-updated-graphql") {
        id
        identifier
        name
        country
        image
        longitude_latitude
        created_at
        updated_at
    }
}
QUERY;
        $response = $this->graphQlQuery($query);
        $this->assertArrayHasKey('shop', $response, 'Expected "shop" key in response');
        $shop = $response['shop'];
        $this->assertEquals("shop-001-updated-graphql", $shop['identifier']);
    }

    /**
     * Test that attempting to delete a shop via GraphQL results in an error.
     */
    public function testDeleteShopGraphQlNotAllowed(): void
    {
        $mutation = <<<MUTATION
mutation {
  deleteShop(id: 1)
}
MUTATION;
        try {
            $this->graphQlMutation($mutation);
            $this->fail('Expected an exception when trying to delete a shop via GraphQL.');
        } catch (GraphQlInputException $e) {
            $this->assertStringContainsString('Deleting shops via API is not allowed.', $e->getMessage());
        }
    }

    /**
     * Test that querying a non-existent shop by identifier returns an error.
     */
    public function testGetShopByIdentifierNotFoundGraphQl(): void
    {
        $query = <<<QUERY
{
    shop(identifier: "non-existent-graphql") {
        id
        identifier
        name
    }
}
QUERY;
        try {
            $this->graphQlMutation($query);
            $this->fail('Expected an exception when shop is not found.');
        } catch (GraphQlInputException $e) {
            $this->assertStringContainsString("'No shop found", $e->getMessage());
        }
    }
}

```

this test class has 5 functional : 
- testGetShopByIdentifierNotFoundGraphQl
- testDeleteShopGraphQlNotAllowed
- testGetShopByIdentifierGraphQl
- testUpdateShopGraphQl

To to able to run these testes we need first to duplicate this src/dev/tests/api-functional/phpunit_graphql.xml.dist into phpunit_graphql.xml.
Then change value of TESTS_BASE_URL to match the value of your store  Url.

And the last step is to execute this command from the racine folder of our projet  :

```bash
vendor/bin/phpunit -c dev/tests/api-functional/phpunit_graphql.xml dev/tests/api-functional/testsuite/Saddemlabidi/ShopfinderGraphQl/ShopGraphQlTest.php
PHPUnit 9.6.21 by Sebastian Bergmann and contributors.
```








