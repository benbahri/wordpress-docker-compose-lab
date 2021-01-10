

Démarrage rapide: Compose et WordPress
======================================

Vous pouvez utiliser Docker Compose pour exécuter facilement WordPress dans un environnement isolé construit avec des conteneurs Docker. Ce guide de démarrage rapide montre comment utiliser Compose pour configurer et exécuter WordPress. Avant de commencer, assurez-vous que [Compose est installé](https://docs.docker.com/compose/install/) .

### Définir le projet

1.  Créez un répertoire de projet vide.

    Vous pouvez nommer le répertoire par quelque chose de facile à retenir. Ce répertoire est le contexte de l'image de votre application. Le répertoire ne doit contenir que des ressources pour créer cette image.

    Ce répertoire de projet contient un `docker-compose.yml` fichier qui est complet en lui-même pour un bon projet wordpress de démarrage.

    > **Conseil** : vous pouvez utiliser une extension `.yml` ou `.yaml` pour ce fichier. Ils fonctionnent tous les deux.

2.  Accédez au répertoire de votre projet.

    Par exemple, si vous avez nommé votre répertoire `my_wordpress` :

    ``` {.highlight}
    cd my_wordpress/

    ```

3.  Créez un `docker-compose.yml` fichier qui démarre votre `WordPress` blog et une `MySQL` instance distincte avec un montage de volume pour la persistance des données:

        version: '3.3'

            services:
               db:
                 image: mysql:5.7
                 volumes:
                   - db_data:/var/lib/mysql
                 restart: always
                 environment:
                   MYSQL_ROOT_PASSWORD: somewordpress
                   MYSQL_DATABASE: wordpress
                   MYSQL_USER: wordpress
                   MYSQL_PASSWORD: wordpress

               wordpress:
                 depends_on:
                   - db
                 image: wordpress:latest
                 ports:
                   - "8000:80"
                 restart: always
                 environment:
                   WORDPRESS_DB_HOST: db:3306
                   WORDPRESS_DB_USER: wordpress
                   WORDPRESS_DB_PASSWORD: wordpress
                   WORDPRESS_DB_NAME: wordpress
            volumes:
                db_data: {}


> **Remarques** :
>
> -   Le volume du docker `db_data` persiste toutes les mises à jour apportées par WordPress à la base de données. [En savoir plus sur les volumes de docker](https://docs.docker.com/storage/volumes/)
>
> -   WordPress Multisite ne fonctionne que sur les ports `80` et `443` .
>
### Construisez le projet

Maintenant, exécutez à `docker-compose up -d` partir du répertoire de votre projet.

Cela s'exécute [`docker-compose up` ](https://docs.docker.com/compose/reference/up/) en mode détaché, extrait les images Docker nécessaires et démarre les conteneurs wordpress et base de données, comme indiqué dans l'exemple ci-dessous.

``` {.highlight}
$ docker-compose up -d
    Creating network "my_wordpress_default" with the default driver
    Pulling db (mysql:5.7)...
    5.7: Pulling from library/mysql
    efd26ecc9548: Pull complete
    a3ed95caeb02: Pull complete
    ...
    Digest: sha256:34a0aca88e85f2efa5edff1cea77cf5d3147ad93545dbec99cfe705b03c520de
    Status: Downloaded newer image for mysql:5.7
    Pulling wordpress (wordpress:latest)...
    latest: Pulling from library/wordpress
    efd26ecc9548: Already exists
    a3ed95caeb02: Pull complete
    589a9d9a7c64: Pull complete
    ...
    Digest: sha256:ed28506ae44d5def89075fd5c01456610cd6c64006addfe5210b8c675881aff6
    Status: Downloaded newer image for wordpress:latest
    Creating my_wordpress_db_1
    Creating my_wordpress_wordpress_1

```

> **Remarque** : WordPress Multisite ne fonctionne que sur les ports `80` et / ou `443` . Si vous obtenez un message d'erreur concernant la liaison `0.0.0.0` au port `80` ou `443`  (selon celui que vous avez spécifié), il est probable que le port que vous avez configuré pour WordPress soit déjà utilisé par un autre service.

### Ouvrez WordPress dans un navigateur Web

À ce stade, WordPress devrait fonctionner sur le port `8000` de votre hôte Docker, et vous pouvez terminer la «fameuse installation de cinq minutes» en tant qu'administrateur WordPress.

> **Remarque** : Le site WordPress n'est pas immédiatement disponible sur le port `8000`  car les conteneurs sont toujours en cours d'initialisation et peuvent prendre quelques minutes avant le premier chargement.

Si vous utilisez [Docker Machine](/machine/) , vous pouvez exécuter la commande `docker-machine ip MACHINE_VM` pour obtenir l'adresse de la machine, puis l'ouvrir `http://MACHINE_VM_IP:8000` dans un navigateur Web.

Si vous utilisez Docker Desktop pour Mac ou Docker Desktop pour Windows, vous pouvez utiliser `http://localhost` comme adresse IP et ouvrir `http://localhost:8000` dans un navigateur Web.

![Choisissez la langue pour l'installation de WordPress](https://docs.docker.com/compose/images/wordpress-lang.png)

![Bienvenue WordPress](https://docs.docker.com/compose/images/wordpress-welcome.png)

### Arrêt et nettoyage

La commande [`docker-compose down`](/compose/reference/down/)supprime les conteneurs et le réseau par défaut, mais préserve votre base de données WordPress.

La commande `docker-compose down --volumes` supprime les conteneurs, le réseau par défaut et la base de données WordPress.
