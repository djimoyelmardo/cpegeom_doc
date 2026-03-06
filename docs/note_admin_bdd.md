
# Administration BDD
# 🗄️ Fiche Administrateur PostgreSQL / PostGIS (SIG) – Version complète structurée

Cette fiche présente les **étapes logiques d’administration PostgreSQL/PostGIS** :
installation → configuration → gestion → stockage → import SIG → sauvegarde → automatisation → monitoring.

---

# 1. Vérification du serveur PostgreSQL

Vérifier l’état du service :

```bash
sudo systemctl status postgresql.service
```

Démarrer le service :

```bash
sudo systemctl start postgresql.service
```

Arrêter :

```bash
sudo systemctl stop postgresql.service
```

Redémarrer :

```bash
sudo systemctl restart postgresql.service
```

Activer au démarrage :

```bash
sudo systemctl enable postgresql.service
```
*N.B. Si le fichier de configuration postgresql.conf a été modifié, faire **sudo systemctl stop postgresql.service** avant de faire **sudo systemctl start postgresql.service***

---

# 2. Connexion à PostgreSQL

Connexion locale :

```bash
psql -U postgres -d postgres
```

Connexion distante :

```bash
psql -h host -p port -U user -d database
```

Exemple :

```bash
psql -h 192.168.10.1 -p 15432 -U editeur -d mardochee
```

Quitter psql :

```sql
\q
```


---

# 3. Gestion des bases de données

Lister les bases :

```sql
\l
```

Créer une base :

```sql
CREATE DATABASE ma_base;
```

Se connecter à  ma base de donnée "mardochee" :

```sql
\c mardochee
```
ou
```sql
\connect mardochee
```
une fois connectée , lister toutes les tables dans le schema "qualite"
```sql
\dt qualite.*
```
lister les utilisateurs de la base de données :
```sql
\du
```

Supprimer une base :

```sql
DROP DATABASE ma_base;
```
---

# 4. Gestion des rôles et utilisateurs
Créer un rôle
```sql
CREATE ROLE nom_utilisateur LOGIN;
```

*Un rôle permet de gérer les utilisateurs et leurs permissions dans la base de données*
Ses pouvoirs : 
- contrôler qui peut se connecter
- définir ce que la personne peut faire
- appliquer une gestion de sécurité et des privilèges

Exemple création d'un rôle "idgeo" :
```sql
-- Role: idgeo (ici idgeo c'est un groupe )
-- DROP ROLE IF EXISTS idgeo;
CREATE ROLE idgeo WITH
    LOGIN -- se connecter à PostgreSQL, Sans LOGIN, ne peut pas ouvrir de session
    NOSUPERUSER --pas es privilèges d'admin global, ne peut pas modifier toute la base ni accéder à toutes les données
    INHERIT --hérite automatiquement des privilèges des rôles auxquels il appartient
    CREATEDB --peut créer de nouvelles bases de données
    NOCREATEROLE -- Interdit au rôle de créer ou modifier d'autres rôles, donc ne peut donc pas gérer les utilisateurs
    NOREPLICATION --ne peut pas être utilisé pour la réplication
    NOBYPASSRLS --ne peut pas contourner les politiques de sécurité au niveau des lignes (Row Level Security)
;
```

Créer un utilisateur :

```sql
CREATE USER user1 WITH PASSWORD 'password';
```

Donner les droits :

```sql
GRANT ALL PRIVILEGES ON DATABASE ma_base TO user1;
```
Revoquer les droits :

```sql
REVOKE  PRIVILEGES ON DATABASE ma_base TO user2;
```

Modifier mot de passe :

```sql
ALTER USER user1 WITH PASSWORD 'new_password';
```

Supprimer utilisateur :

```sql
DROP USER user1;
```

Lister les rôles :

```sql
\du
```

---

# 5. Installation de PostGIS

Activer PostGIS :

```sql
CREATE EXTENSION postgis;
```

Vérifier la version :

```sql
SELECT PostGIS_version();
```

Lister les extensions :

```sql
\dx
```

---

# 6. Gestion des schémas

Créer un schéma :

```sql
CREATE SCHEMA admin;
```

Lister les schémas :

```sql
\dn
```

Supprimer :

```sql
DROP SCHEMA admin CASCADE;
```

---

# 7. Création de tables géographiques

```sql
CREATE TABLE communes (
    id SERIAL PRIMARY KEY,
    nom VARCHAR(100),
    geom geometry(MULTIPOLYGON,2154)
);
```

---

# 8. Index spatial

```sql
CREATE INDEX communes_geom_idx
ON communes
USING GIST (geom);
```

---

# 9. Fonctions PostGIS essentielles

```
ST_Intersects()
ST_Contains()
ST_Distance()
ST_Buffer()
ST_Union()
ST_Area()
ST_Length()
ST_Transform()
```

Exemple :

```sql
SELECT *
FROM communes
WHERE ST_Intersects(geom, autre_geom);
```

---

# 10. Transformation de projection

```sql
SELECT ST_Transform(geom,2154)
FROM table;
```

---

# 11. Tablespaces PostgreSQL

Créer le dossier du tablespace :

```bash
sudo mkdir -p /srv/tablespace/
```

Donner les droits :

```bash
sudo chown postgres:postgres /srv/tablespace/
```

Créer le tablespace :

```sql
CREATE TABLESPACE mine
LOCATION '/srv/tablespace';
```

Lister les tablespaces :

```sql
\db
```

---

# 12. Vérification du stockage physique

Lister le contenu :

```bash
sudo ls -la /srv/tablespace
```

Explorer la structure interne :

```bash
sudo ls -la /srv/tablespace/PG_*
```

Voir les liens PostgreSQL :

```bash
ls -la /var/lib/postgresql/16/main/pg_tblspc
```

---

# 13. Import de données SIG

Importer un shapefile :

```bash
ogr2ogr -f "PostgreSQL" PG:"dbname=ma_base user=postgres" -nln admin.communes communes.shp
```

Importer avec reprojection :

```bash
ogr2ogr -f "PostgreSQL" PG:"dbname=ma_base user=postgres" -s_srs EPSG:4326 -t_srs EPSG:2154 communes.shp
```

Importer un département spécifique :

```bash
ogr2ogr -f "PostgreSQL" PG:"dbname=hg user=postgres" departement_31.shp -nln dept_31 -nlt PROMOTE_TO_MULTI -lco GEOMETRY_NAME=geom
```

---

# 14. Export de données

```bash
ogr2ogr communes.shp PG:"dbname=ma_base user=postgres" -nln admin.communes
```

---

# 15. Sauvegarde PostgreSQL

Sauvegarde simple :

```bash
pg_dump ma_base > sauvegarde.sql
```

Sauvegarde complète :

```bash
pg_dumpall > all_db.sql
```

Sauvegarde avec options :

```bash
pg_dump -U postgres -p 5432 -d hg -f /mnt/d/admin_bd/hg_backup.sql
```

---

# 16. Restauration d'une base

Créer une base :

```bash
psql -U postgres -p 5432 -c "CREATE DATABASE hg_restore;"
```

Restaurer :

```bash
psql -U postgres -p 5432 -d hg_restore < hg_backup.sql
```

---

# 17. Script bash de sauvegarde automatisée

Créer le script :

```bash
nano script_sauv_bdd.sh
```

Script :

```bash
#!/bin/bash

DATE=$(date +%Y-%m-%d_%H-%M)

pg_dump -U postgres -p 5432 -d hg -f /mnt/d/admin_bd/hg_backup_$DATE.sql

psql -U postgres -c "DROP DATABASE IF EXISTS hg_restore;"
psql -U postgres -c "CREATE DATABASE hg_restore;"

psql -U postgres -p 5432 -d hg_restore < /mnt/d/admin_bd/hg_backup_$DATE.sql
```

Rendre exécutable :

```bash
chmod +x script_sauv_bdd.sh
```

Tester :

```bash
./script_sauv_bdd.sh
```

---

# 18. Automatisation avec CRON

Modifier la crontab :

```bash
crontab -e
```

Ajouter :

```bash
20 3 * * * /home/ofbpnmba/script_sauv_bdd.sh >> /home/ofbpnmba/backup.log 2>&1
```

Vérifier :

```bash
crontab -l
```

---

# 19. Monitoring PostgreSQL

Tester si PostgreSQL répond :

```bash
pg_isready
```

Lister les clusters :

```bash
pg_lsclusters
```

Voir les processus :

```bash
ps -aux | grep postgres
```

Surveiller les ressources :

```bash
htop
```

Connexions actives :

```sql
SELECT * FROM pg_stat_activity;
```

---

# 20. Logs PostgreSQL

Consulter les logs :

```bash
sudo less /var/log/postgresql/postgresql-16-main.log
```

Suivre en temps réel :

```bash
tail -f /var/log/postgresql/postgresql-16-main.log
```

---

# 21. Export de l'historique Linux

Afficher l’historique avec date :

```bash
HISTTIMEFORMAT="%F %T " history
```

Exporter dans un fichier CSV :

```bash
HISTTIMEFORMAT="%F;%T " history | grep "$(date +%F)" > history_2026.csv
```

---

# 22. Architecture SIG typique

```
Linux
 ├─ PostgreSQL
 │   └─ PostGIS
 ├─ GDAL / OGR
 ├─ QGIS
 └─ WebSIG
     ├─ GeoServer
     ├─ Leaflet
     └─ OpenLayers
```

---

# 23. Bonnes pratiques DBA SIG

✔ utiliser des tablespaces pour les gros volumes  
✔ créer des index spatiaux  
✔ vérifier les projections EPSG  
✔ sauvegarder régulièrement la base  
✔ automatiser les sauvegardes avec cron  
✔ surveiller les logs PostgreSQL


# 🔗 Connexions distantes avec Foreign Data Wrappers (FDW)

## 1. Principe

Les **Foreign Data Wrappers (FDW)** permettent à PostgreSQL d'accéder à des **données externes** comme si elles étaient des **tables locales**.

Cela permet de :

- interroger des **bases PostgreSQL distantes**
- accéder à des **services web (WFS)**
- lire des **fichiers externes**
- accéder à d'autres bases de données (Oracle, MySQL, etc.)

Les données externes apparaissent sous forme de **foreign tables**.

---

# 2. Avantages des FDW

✔ accès transparent aux données distantes  
✔ possibilité de **requêtes SQL directes**  
✔ intégration avec **PostGIS**  
✔ évite la duplication des données  

Exemple :

```sql
SELECT *
FROM table_distante
WHERE id = 10;
```

---

# 3. Types de sources supportées

Le FDW permet d'accéder à différents types de sources :

| Source | Mode |
|------|------|
PostgreSQL | lecture / écriture |
Oracle | lecture / écriture |
MySQL | lecture / écriture |
CSV | lecture |
ODBC / JDBC | dépend du driver |
NoSQL (CouchDB, Redis) | dépend du driver |
WFS / fichiers géographiques | lecture |

---

# 4. Activation des extensions FDW

Activer le FDW PostgreSQL :

```sql
CREATE EXTENSION postgres_fdw;
```

Activer le FDW GDAL :

```sql
CREATE EXTENSION ogr_fdw;
```
Activer le FDW FILE GDAL :

```sql
CREATE EXTENSION file_fdw;
```
---

# 5. Connexion à une base PostgreSQL distante

## Création du serveur distant

```sql
CREATE SERVER foreign_bd
FOREIGN DATA WRAPPER postgres_fdw
OPTIONS (
    host 'localhost',
    port '5434',
    dbname 'bd_test_postgis'
);
```

| Paramètre | Description |
|------|------|
host | adresse du serveur distant |
port | port PostgreSQL |
dbname | base distante |

---

# 6. Association des utilisateurs

Associer un utilisateur local à un utilisateur distant.

```sql
CREATE USER MAPPING FOR postgres
SERVER foreign_bd
OPTIONS (
    user 'postgres',
    password 'postgres'
);
```

| élément | description |
|------|------|
postgres | utilisateur local |
user | utilisateur distant |
password | mot de passe distant |

---

# 7. Création d'une table distante

Une **foreign table** représente une table distante.

```sql
CREATE FOREIGN TABLE foreign_parkings (
    osm_id integer NOT NULL,
    date_heure timestamp without time zone,
    nom character varying,
    type_obj character varying,
    xcoord double precision,
    ycoord double precision,
    geom geometry(Point,2154)
)
SERVER foreign_bd
OPTIONS (
    schema_name 'public',
    table_name 'parkings'
);
```

Interroger la table distante :

```sql
SELECT *
FROM foreign_parkings
LIMIT 10;
```

---

# 8. Accès à un service WFS avec ogr_fdw

Créer un serveur WFS :

```sql
CREATE SERVER fdw_ogr_inpn_metropole
FOREIGN DATA WRAPPER ogr_fdw
OPTIONS (
    datasource 'WFS:http://ws.carmencarto.fr/WFS/119/fxx_inpn?',
    format 'WFS'
);
```

---

# 9. Création du schéma d'import

```sql
CREATE SCHEMA IF NOT EXISTS inpn_metropole;
```

---

# 10. Import des tables du service WFS

```sql
IMPORT FOREIGN SCHEMA ogr_all
FROM SERVER fdw_ogr_inpn_metropole
INTO inpn_metropole
OPTIONS (
    launder_table_names 'true',
    launder_column_names 'true'
);
```

| option | rôle |
|------|------|
launder_table_names | nettoie les noms de tables |
launder_column_names | nettoie les noms de colonnes |

---

# 11. Vérification des tables importées

```sql
SELECT foreign_table_schema, foreign_table_name
FROM information_schema.foreign_tables
WHERE foreign_table_schema = 'inpn_metropole'
ORDER BY foreign_table_schema, foreign_table_name;
```

---

# 12. Exemple d'interrogation

```sql
SELECT *
FROM inpn_metropole.zico
LIMIT 3;
```

---

# 13. Architecture FDW

```
PostgreSQL local
        │
        │
        ├── postgres_fdw
        │       │
        │       └── PostgreSQL distant
        │
        └── ogr_fdw
                │
                └── WFS / fichiers géographiques
```

---

# 14. Bonnes pratiques

✔ utiliser FDW pour éviter la duplication des données  
✔ limiter les requêtes lourdes sur les serveurs distants  
✔ créer des vues locales pour simplifier l'accès  
✔ surveiller les performances réseau

---

# 15. Exemple d'utilisation SIG

```
Serveur SIG
│
├── Base principale
│
├── FDW vers base cadastre
│
├── FDW vers base biodiversité
│
└── FDW vers service WFS INPN
```

Les données peuvent être utilisées directement dans **QGIS**.
