  app:
    image: php:8.2-fpm  # Utilise l'image officielle PHP 8.2 avec FPM (FastCGI Process Manager)
    container_name: symfony_app  # Nom du conteneur
    working_dir: /var/www/html  # Définition du répertoire de travail
    volumes:
      - ./app:/var/www/html  # Monte le dossier local `app` dans le conteneur
    networks:
      - symfony_network  # Connecte le service au réseau Docker "symfony_network"

  webserver:
    image: nginx:stable  # Utilise l'image officielle Nginx
    container_name: symfony_webserver  # Nom du conteneur
    ports:
      - "8080:80"  # Redirige le port 8080 de la machine vers le port 80 du conteneur
    volumes:
      - ./app:/var/www/html  # Monte le dossier `app` dans le conteneur
      - ./nginx:/etc/nginx/conf.d  # Monte le dossier de configuration Nginx
    depends_on:
      - app  # Démarre seulement après `app`
    networks:
      - symfony_network

        database:
    image: mysql:8.0  # Utilise l'image officielle MySQL 8.0
    container_name: symfony_db  # Nom du conteneur
    environment:
      PMA_HOST: symfony_db  # Déclare l'hôte MySQL pour Adminer/phpMyAdmin
      MYSQL_ROOT_PASSWORD: root  # Mot de passe root de MySQL
      MYSQL_DATABASE: symfony  # Crée une base de données `symfony`
      MYSQL_USER: symfony  # Nom d'utilisateur
      MYSQL_PASSWORD: symfony  # Mot de passe utilisateur
    ports:
      - "3306:3306"  # Expose MySQL sur le port 3306
    volumes:
      - db_data:/var/lib/mysql  # Stocke les données MySQL de manière persistante
    networks:
      - symfony_network

  adminer:
    image: adminer  # Utilise l'image officielle Adminer
    container_name: symfony_adminer  # Nom du conteneur
    restart: always  # Redémarre automatiquement en cas d'arrêt
    ports:
      - "8081:8080"  # Adminer accessible via `http://localhost:8081`
    depends_on:
      - database  # Démarre après `database`
    networks:
      - symfony_network

  phpmyadmin:
    image: phpmyadmin/phpmyadmin  # Utilise l'image officielle phpMyAdmin
    container_name: symfony_phpmyadmin  # Nom du conteneur
    restart: always  # Redémarre automatiquement en cas d'arrêt
    ports:
      - "8082:80"  # phpMyAdmin accessible via `http://localhost:8082`
    environment:
      PMA_HOST: symfony_db  # Indique que MySQL est sur `symfony_db`
      MYSQL_ROOT_PASSWORD: root  # Mot de passe root pour se connecter
      PMA_PMADB: phpmyadmin
      PMA_CONTROLUSER: symfony
      PMA_CONTROLPASS: symfony
    depends_on:
      - database  # Démarre après `database`
    networks:
      - symfony_network

      networks:
  symfony_network:
    driver: bridge  # Utilise un réseau en mode bridge (standard)

volumes:
  db_data:  # Stocke les données MySQL
  phpmyadmin_data:  # (Non utilisé ici mais peut être utile pour stocker les préférences phpMyAdmin)


