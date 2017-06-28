### API with PHP

Toda la documentacion de la **YouTube Data API (v3) para PHP** la encuentras [aqui](https://developers.google.com/youtube/v3/code_samples/php).

Antes de poder utilizar la API es necesario descargar la [Biblioteca cliente de API de Google para PHP](https://github.com/google/google-api-php-client/tree/v1-master) desde los respositorios de GitHub de google.

Clonamos o descargamos la librearia y unicamente vamos a hacer uso de la carpeta ***src/Google*** la cual contiene todas las librearias de las APIs de google.

Para esta aplicacion vamos a usar el ejemplo **[Recuperar mis videos subidos](https://developers.google.com/youtube/v3/code_samples/php#retrieve_my_uploads)** que se encuentra en el sitio de la API.

Las claves de autorizacion que se crearon anteriormente se deben indicar en las siguientes variables:
```php
$OAUTH2_CLIENT_ID = 'REPLACE_ME';
$OAUTH2_CLIENT_SECRET = 'REPLACE_ME';
```

[CODE: ]
```php
<?php

// Call set_include_path() as needed to point to your client library.
require_once 'Google/Client.php';
require_once 'Google/Service/YouTube.php';
session_start();

/*
 * You can acquire an OAuth 2.0 client ID and client secret from the
 * Google Developers Console <https://console.developers.google.com/>
 * For more information about using OAuth 2.0 to access Google APIs, please see:
 * <https://developers.google.com/youtube/v3/guides/authentication>
 * Please ensure that you have enabled the YouTube Data API for your project.
 */
$OAUTH2_CLIENT_ID = 'REPLACE_ME';
$OAUTH2_CLIENT_SECRET = 'REPLACE_ME';

$client = new Google_Client();
$client->setClientId($OAUTH2_CLIENT_ID);
$client->setClientSecret($OAUTH2_CLIENT_SECRET);
$client->setScopes('https://www.googleapis.com/auth/youtube');
$redirect = filter_var('http://' . $_SERVER['HTTP_HOST'] . $_SERVER['PHP_SELF'],
  FILTER_SANITIZE_URL);
$client->setRedirectUri($redirect);

// Define an object that will be used to make all API requests.
$youtube = new Google_Service_YouTube($client);

if (isset($_GET['code'])) {
  if (strval($_SESSION['state']) !== strval($_GET['state'])) {
    die('The session state did not match.');
  }

  $client->authenticate($_GET['code']);
  $_SESSION['token'] = $client->getAccessToken();
  header('Location: ' . $redirect);
}

if (isset($_SESSION['token'])) {
  $client->setAccessToken($_SESSION['token']);
}

// Check to ensure that the access token was successfully acquired.
if ($client->getAccessToken()) {
  try {
    // Call the channels.list method to retrieve information about the
    // currently authenticated user's channel.
    $channelsResponse = $youtube->channels->listChannels('contentDetails', array(
      'mine' => 'true',
    ));

    $htmlBody = '';
    foreach ($channelsResponse['items'] as $channel) {
      // Extract the unique playlist ID that identifies the list of videos
      // uploaded to the channel, and then call the playlistItems.list method
      // to retrieve that list.
      $uploadsListId = $channel['contentDetails']['relatedPlaylists']['uploads'];

      $playlistItemsResponse = $youtube->playlistItems->listPlaylistItems('snippet', array(
        'playlistId' => $uploadsListId,
        'maxResults' => 50
      ));

      $htmlBody .= "<h3>Videos in list $uploadsListId</h3><ul>";
      foreach ($playlistItemsResponse['items'] as $playlistItem) {
        $htmlBody .= sprintf('<li>%s (%s)</li>', $playlistItem['snippet']['title'],
          $playlistItem['snippet']['resourceId']['videoId']);
      }
      $htmlBody .= '</ul>';
    }
  } catch (Google_Service_Exception $e) {
    $htmlBody .= sprintf('<p>A service error occurred: <code>%s</code></p>',
      htmlspecialchars($e->getMessage()));
  } catch (Google_Exception $e) {
    $htmlBody .= sprintf('<p>An client error occurred: <code>%s</code></p>',
      htmlspecialchars($e->getMessage()));
  }

  $_SESSION['token'] = $client->getAccessToken();
} else {
  $state = mt_rand();
  $client->setState($state);
  $_SESSION['state'] = $state;

  $authUrl = $client->createAuthUrl();
  $htmlBody = <<<END
  <h3>Authorization Required</h3>
  <p>You need to <a href="$authUrl">authorize access</a> before proceeding.<p>
END;
}
?>

<!doctype html>
<html>
  <head>
    <title>My Uploads</title>
  </head>
  <body>
    <?=$htmlBody?>
  </body>
</html>
```

Ejecutamos nuestra APP en nuestro navegador y lo primero que nos solicitara es **Autorizar la aplicacion**
![capture-php-01](/img/capture-php-01.png)
![capture-php-02](/img/capture-php-02.png)


### ISSUES
1.- Al ejecutar nuestra aplicacion en un servidor, ya sea local o publico nos encontraremos con este error.
![capture-php-03](/img/capture-php-03.png)

Dicho error se soluciona agregando el autoload.php mismo que se encuentra en los archivos descargados de la [Biblioteca cliente de API de Google para PHP](https://github.com/google/google-api-php-client/tree/v1-master).

[REFERENCE](https://stackoverflow.com/questions/28351680/implementing-oauth2-login-fatal-error-class-google-service-not-found/34913637#34913637)

```php
require_once "Google/autoload.php";
```

2.- El segundo error que se muestra es **Notice: Undefined variable: htmlBody in C:\xampp\htdocs\yt\index.php on line 96**
![capture-php-04](/img/capture-php-04.png)

Dicho error se soluciona inicializando la variable `$htmlBody` de la siguiente manera

```php
$htmlBody = '';
```


### Sugerencia
En el sitio [DOM SAMMUT](https://www.domsammut.com/code/php-server-side-youtube-v3-oauth-api-video-upload-guide) encontraras un tutorial mas detallado y un ejemplo de codigo recomendado, en el que indica todos los parametros que se pueden obtener apartir de la API.