# Laravel AFIP Web Service Wrapper

AFIP technical documentation for their Web Service [AFIP Web Service](http://www.afip.gob.ar/ws/paso4.asp?noalert=1)


## Installation

Type in your terminal:

```
composer require hazzo/LaravelAfipWrapper
```

Register AfipWrapper by adding it to the service providers on the `app.php`.
```
'providers' => array(
        ...
        hazzo\LaravelAfipWrapper\Providers\AfipWsServiceProvider::class
    )
```

Let's add the facade in the bottom of the same file in aliases.
```
'aliases' => array(
        ...
        'AfipWS' => hazzo\LaravelAfipWrapper\Facades\AfipWs::class,
    )
```
    
Publish the package configuration file.

```
php artisan vendor:publish
```

### Configuration

Config file options:

| Option            | Default      | Values     |
| :---------------      | :-------  | :--------- |
|  `afipTempFolder`     |  '' (Automatically the package will choose */temp* as folder in root directory)    | Any string. Ex.:  `storage_path('app/afip')`|
|  `afipEnvironment`    | '' (Automatically the package will choose *DEV* environment) | Two options `DEV` or `PROD`, for homologation and production endpoints for AFIP Web Service. |

##  Usage samples

Generate **token** and **sign** to get auth data and perform later actions.

Options `array` for method `generateLTR` *(LoginTicketRequest)*:

* `cuit`: Cuit of user that is going to use the web service
* `cn`: *commonName* field of user that is going to use the web service
* `id`: 32 bit digit that together with `genTime` will be used by the web service to identify the request
* `privateKey`: Path where the private key generated by the AFIP site is stored
* `pemCert`: Path where the CSR Certificate used to generate the private key is stored
* `genTime`: Generation time of the authentication. Must be ISO8601 formatted. If not given the pacakge will set it to *today*
* `expTime`: Expiration time of the authentication. Must be ISO8601 formatted. If not given the pacakge will set it to *today* + 24hs. The max tolerance for expiration is 24hs from the generation time.

```
use hazzo\LaravelAfipWrapper\AfipWs;
   
   $afipWs = new AfipWs();
   
   $options = [
       'cuit' => '2020200200',
       'cn' => 'test',
       'id' => 1,
       'privateKey' => './AfipPrivateKey.key',
       'pemCert' => './MyPEMKey.crt'
   ];
   
   $ltr = $afipWs->generateLTR($options);
   $afipWs->generateTRA($ltr);
   
   var_dump($afipWs->token);
   var_dump($afipWs->sign);
   var_dump($afipWs->cuit);
   
```
