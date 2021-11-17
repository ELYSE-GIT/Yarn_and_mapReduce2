NOM et prénoms : RASOLOARIVONY Miadantsoa Elysé
Big Data Framework I
Titre du LAB : HDFS 
Année : 2021/2022


#### Travaillons sur le local : 

Création d’un nouveau fichier texte :
cat > bonjour.txt

Modification du contenu du fichier bonjour.txt :
nano bonjour.txt

Vérification de la modification du fichier texte :
cat  bonjour.txt

Travaillons sur HDFS : 

# KERBEROS 

##### Initialisation du ticket Kerberos avec  *kinit elyse.rasoloarivony*
J’ai entré à nouveau mon mot de passe (pour que l’ « Authentication Server et le Ticket Granting Server» arrivent à décrypter ma requête afin que je puisse accéder à tous les contenus du HDFS, autrement dit, afin que je puisse obtenir un « Token » pour accéder au contenu)


### Commandes basiques de HDFS: 

##### Maintenant, on peut travailler sur un système de fichier distribuer. 
Deux comptes sont concernés :
1 – Mon compte local Linux (nommé local par Hadoop)
2 – Mon compte sur HDFS

NB : On aura accès à ces 2 comptes en fonction des syntaxes utilisés : 
```bash
* Pour Linux : ls, cat, mkdir, cd, etc.

* Pour HDFS : hdfs dfs -ls [ -R -h / : pour voir tous les sous dossiers/contenus ], hdfs dfs -mkdir, etc. (Il n’y a pas de commande cd , on doit toujours mettre le chemin) 
```

* Création du dossier (toujours de taille 0) :
> hdfs dfs -mkdir dossier

* Copions bonjour.txt dans HDFS :
> hdfs dfs -put bonjour.txt 

* Pour voir le contenu de bonjour.txt copié dans HDFS :
> hdfs dfs -cat bonjour.txt 

On a bien le même fichier qu’on a crée dans le serveur local*

* Pour voir le dernier KB du fichier :
> hdfs dfs -tail bonjour.txt 

* Pour supprimer bonjour.txt :
> hdfs dfs -tail bonjour.txt 

* Maintenant, utilisons -copyFromLocal à la place de -put :
> hdfs dfs -copyFromLocal bonjour.txt

* Pour déplacer bonjour.txt dans dossier : 
> hdfs dfs -mv bonjour.txt dossier/bonjour.txt

* Transférer un fichier HDFS dans mon local en changeant son nom dans la destination (déconseillé dans le big data):
> hdfs dfs -get dossier/bonjour.txt nouveau.txt

* Copier un fichier dans HDFS :
> hdfs dfs -cp dossier/bonjour.txt dossier/salut.txt 

* Affichage du nombre des sous dossiers fichiers et les places occupées dans le chemin « / » : 
> hdfs dfs -count -h / 
* Pour supprimer un :
    * Fichier :
    > hdfs dfs -rm dossier/bonjour.txt
    * Dossier :
    > hdfs dfs -rm -r -skipTrash dossier



# Uploading a file to HDFS :

Téléchargeons un des e-book sous format (in Plain Text UTF8) sur www.gutenberg.org/wiki/Main_Page
Mon choix : https://www.gutenberg.org/cache/epub/66464/pg66464.txt



* Création d’un dossier raw dans HDFS :
> hdfs dfs -mkdir raw


* Put the downloaded e-book inside raw
```
Etape 1 : Importer le fichier dans le local : wget https://www.gutenberg.org/cache/epub/66464/pg66464.txt


Etape 2 : copier le fichier importé dans HDFS 
hdfs dfs -put pg66464.txt

Etape 3 : déplacer le fichier du « root » dans le dossier raw :
hdfs dfs -mv pg66464.txt raw/pg66464.txt
```


* Copier directement dans hdfs, dans le dossier home
> hdfs dfs -cp raw/pg66464.txt /


* On souhaite renommer pg66464.txt par input.txt
```
Etape 1 : On copie le fichier du HDFS dans le local, en changeant son nom avec :
hdfs dfs -get pg66464.txt input.txt

Etape 2 : On copie le fichier input.txt du local vers HDFS : 
hdfs dfs -put input.txt

Etape 3 : On supprime le fichier original dans « home »
hdfs dfs -rm pg66464.txt
```

* Lecture directe de input.txt
> hdfs dfs -cat input.txt


* Suppression de input.txt :
> hdfs dfs -rm input.txt

* Les contenus de mon dossier Home :
> hdfs dfs -ls 

* Pour copier le fichier de HDFS dans le local
> hdfs dfs -get raw/pg66464.txt local.txt 

*Remarque : get = copyToLocal

wget : permet de télécharger directement un fichier depuis internet dans le edge
scp (unix) ou filezilla (windows) : permettent de transférer des fichier de mon pc vers edge


# Très instructif !