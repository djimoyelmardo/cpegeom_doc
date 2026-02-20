# Interoprabilité
## Définition
Système capable de fonctionner avec d’autres produits ou systèmes existants ou futurs, sans restriction d’accès ou de mise en oeuvre.

**Compatibilité** : notion verticale faisant qu’un outil peut fonctionner dans un environnement donné

**Interopérabilité** : notion transversale permettant à des outils de pouvoir communiquer et fonctionner ensemble.

## Les enjeux de l'interopérabilité
L'interopérabilité en géomatique se concentre sur la capacité des données spatiales et des systèmes d'information géographique (SIG) à être compréhensibles, compatibles et réutilisables entre diverses sources et applications. Voici quelques aspects clés de l'interopérabilité en géomatique selon les informations fournies :
<ul>
    <li>Sémantique : Définition standardisée des concepts, de la structure et du sens des données spatiales grâce à des modèles.
    =>une couche dans QGIS, dans PostGIS, dans GPKG
    => une classe d'entité dans ArcGIS</li>
    <li>Géographique** : Coordonnées géographiques basées sur des systèmes géodésiques uniformes, telles que le RGF93 en France.</li>
    <li>**Standards et spécifications** : Organisations telles que l'Open Geospatial Consortium (OGC) développent et promeuvent des standards ouverts pour assurer l'interopérabilité des contenus, des services et des échanges dans les domaines de la géomatique et de l'information géographique.</li>
    <li>**Directives et recommandations** : Des initiatives européennes comme la directive INSPIRE encouragent l'interopérabilité des données spatiales et facilitent leurs échanges et leur réutilisation.</li>
    <li>**Partenariats et collaborations** : Collectivités territoriales et organisations privées travaillent ensemble pour garantir l'interopérabilité des données et les facilites de partage, tels que le cas de la Métropole Rouen Normandie.</li>
</ul>

## Des normes
<div style="display: flex; align-items: center; gap: 50px; margin: 40px 0;">
  <div>
    <p>
      ISO / TC 211
      L'Organisation internationale de normalisation (dont le nom court est ISO) est un organisme de normalisation international composé de représentants l'organisations nationales de normalisation de 165 pays.
      Cette organisation créée en 1947 a pour but de produire des normes internationales dans les domaines industriels et commerciaux appelées normes ISO.
      L'Organisation internationale de normalisation se compose de plus de 200 comités techniques. 
      Processus d’élaboration des normes et produits : 
    </p>
    <ul>
        <li>Proposition,</li>
        <li>Préparation,</li>
        <li>Comité,</li>
        <li>Enquête,</li>
        <li>Approbation,</li>
    </ul>   
  </div>
</div>

[Lien normalisation en SIG](https://www.iso.org/fr/committee/54904/x/catalogue/)

Ces normes peuvent spécifier, pour l'information géographique, des méthodes, outils et services pour la gestion de données (y compris leur définition et leur description), l’acquisition, le traitement, l'analyse, l'accès, la présentation et le transfert de ces données sous forme numérique / électronique entre les différents utilisateurs, systèmes et sites.

[La directive INSPIRE](https://inspire-geoportal.ec.europa.eu/srv/fre/catalog.search#/home)

## Open Geospatial Consortium (OGC)
Organisation internationale à but non lucratif dédiée au développement des systèmes ouverts en géomatique fondé en 994 pour répondre aux problèmes de non interopérabilité des applications concernant l'information géographique 
### Les missions
<ul>
    <li>regrouper les acteurs concernés afin de développer et promouvoir les standards ouverts,</li>
    <li>garantir l'interopérabilité dans le domaine de la géomatique,</li>
    <li>favoriser la coopération entre développeurs, fournisseurs et utilisateurs,</li>
    <li>permettre de réaliser des systèmes et des services d'information complexes et ouverts,</li>
    <li>impliquer l’ensemble de la communauté dans le processus d’interopérabilité,</li>
</ul>

[Lien ](https://www.ogc.org/)

### Des ressources
<ul>
    <li>Une plateforme (le site),</li>
    <li>les standards internationaux assurant l'interopérabilité,</li>
    <li>des retours d'expériences et bonnes pratiques,</li>
    <li>une communauté de membres,</li>
    <li>des rapports et publications scientifiques,</li>
</ul>

## Les Géo standards pour une bonne diffusion
### Le coeur de FAIR
Findable, Accessible, Interoperable et Reusable
Trouvable, Accessible, Interopérable et Réutilisable
**En évolution permanente** => âge du WMS vs âge du COG, plus de 30 ans d'écarts
###  Les Web Services (WMS, WFS, WMTS, WCS, WFS-T...)
<ul>
    <li>Protocoles HTTP (Hyper Text Transfer Protocol) fournissant des images et données géoréférencées par des URL (Uniform Ressource Location) selon des normes standardisées.</li>
    <li>Les données sont mises à disposition par des serveurs cartographiques (Geoserver, Mapserver) et directement lues sur un navigateur web grâce à un client carto (OpenLayers) ou sur un logiciel desktop client (Qgis, Arcgis).</li>
</ul>

## Les standards de l’OGC
### Des Webservices (distant)
<ul>
    <li>WMS - Web Map ServiceProposition: À l'ouverure dans un éditeur, un WMS s'affiche au format xml. Il renvoie une seule image sur l'étendue demandée et s'affiche plus lentement
    https://data.geopf.fr/wms-r/wms?
    VERSION=1.3.0&
    SERVICE=WMS&
    REQUEST=GetCapabilities
    </li>
    <li>WMTS - Web Map Tile Service  :  Renvoie la tuile sur l'étendue de l'image demandée et s'affcihe plus vite
    https://data.geopf.fr/wmts?
    SERVICE=WMTS&
    REQUEST=GetTile&
    VERSION=1.0.0&
    LAYER=HR.ORTHOIMAGERY.ORTHOPHOTOS&
    STYLE=normal&
    FORMAT=image%2Fjpeg&
    TILEMATRIXSET=PM_6_19&
    TILEMATRIX=14&
    TILEROW=5638&
    TILECOL=8296
    </li>
    <li>WFS - Web Feature Service : la strucure du lien WFS
    https://data.geopf.fr/wfs/wfs?
    SERVICE=WFS&
    REQUEST=GetFeature&
    VERSION=2.0.0&TYPENAMES=OCS-GERS_BDD_LAMB93_2019:oscge_gers_32_2019&STARTINDEX=40000&COUNT=5000&SRSNAME=urn:ogc:def:crs:EPSG::2154&
    BBOX=-118375001.75732161104679108,-127711935.79784440994262695,119759640.55220197141170502,111272176.1037464439868927,urn:ogc:def:crs:EPSG::2154
    </li>
    <li>TMS - Tile Map Service : 
    https://tile.openstreetmap.org/15/16592/11269.png
    </li>
    <li>WCS - Web Coverage Service</li>
    <li>CS-W - Catalog Service Web</li>
    <li>WPS - Web Processing Service</li>
</ul>

### Des formats (en local)
<ul>
    <li>SLD - Style Encoding Description</li>
    <li>GML - Geography Markup Language</li>
    <li>KML - Keyhole Markup Language</li>
    <li>GPKG - Geopackage</li>
    <li>WKT CRS – Notation des CRS WKT</li>
</ul>

## Le cataloge et les métadonnées
La notion de diffusion de donnée a été confirmée par la possibilité de partager des données de qualité.
<ul>
    <li>Des données de qualité => exhaustives, propres et géométriquement correctes</li>
    <li>Des données documentées => fiches de métadonnées suivant des normes de rédaction (ISO 19115 / 19139, INSPIRE, DCAT)</li>
    <li>Des données diffusables => catalogue de données / métadonnées, Catalogue Service Web CSW</li>
    <li>Des données réutilisables => accessibles, ouvertes</li>
</ul>

## Extract Transform Load ETL
 Un outils fiable => le traitement automatisé. 
![](./images/etl.png)
**Extact**: le processus d'extraction doit convertir les données dans un format adapté à une transformation ultérieure
**Transform** : Nettoyage, Filtrage, Enrichissement, Division, (spliting), Regroupement.
**Load** : Les données transformées peuvent être chargé dans la base cible.

### Spatial ETL
L’ETL Spatial, aussi connu sous l’appellation GTL (« Geographic Transformation and Load »), propose les mêmes fonctionnalités que l’ETL, appliquées à la manipulation des données géographiques.
![](./images/spatial_etl.png)
L’ETL spatial permet :
<ul>
    <li>De synchroniser des bases entre-elles,</li>
    <li>De traduire des jeux de données géographiques d’un format vers un autre (OGR / GDAL),</li>
    <li>De restructurer complètement le modèle de données et l’adapter au modèle cible, à l’aide d’unités de traitement (transformers chez FME) qui modifient la structure, les attributs et la géométrie des entités.</li>
</ul>

### Transformations géospatiales communes
<ul>
    <li>Reprojection : possibilité de convertir des données spatiales entre un système de coordonnées et un autre</li>
    <li>Transformations spatiales : possibilité de modéliser des interactions spatiales et de calculer des prédicats spatiaux.</li>
    <li>Transformations topologiques : possibilité de créer des relations topologiques entre des ensembles de données disparates.</li>
    <li>Re-symbolisation : la possibilité de modifier les caractéristiques cartographiques d'une entité, comme la couleur ou le style de trait.</li>
    <li>Géocodage : possibilité de convertir des attributs de données tabulaires en données spatiales.</li>
</ul>
![](./images/etl_traitement_ndvi.png)

### Les formats de données
Cette liste n'est pas forcement exhaustive et beaucoup de formats ne sont pas ou peu utilisés dans la pratique (logiciels obsolètes, formats non utilisés en France)
[Georezo a listé tous les formats utilisés](https://georezo.net/wiki/main/donnees/extensions)