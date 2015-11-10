# Introduction #

Le but de ce plugin nagios est de télécharger une page web avec tout ses liens ( interne ).

Un processus fils est créé par ressource et toutes sont téléchargées en parallèle.

# Fonctionalités #

  * Petit script Ruby, facile à comprendre et modifier
  * http/https
  * Utilisation de la lib hpricot pour parser le html
  * Multi-threads
  * Recherche de mot clef
  * Suit les redirections
  * Télécharge tous les liens de la page ou juste le HTML


# Dépendances #
  * Testé avec Ruby
    * 1.8.7
    * 1.9.2
  * [hpricot](http://wiki.github.com/why/hpricot)
  * [optiflag](http://optiflag.rubyforge.org/)

# Usage #
```
$ ./check_webpage.rb -h

Help for commands:
  -w          (Optionnel, prend 1 argument)
                  --warn, defaut 5,  warn time (s)
  -e          (Optionnel, prend 0 argument)
                  mode étendu, voir la documentation
  -k          (Optionnel, prend 1 argument)
                  --key, cherche un mot clef
  -h          (Optionnel, prend 0 argument)
                  Aide
  -u          (Requis, prend 1 argument)
                  --url, absolut: [http://www.google.com]
  -c          (Optionnel, prend 1 argument)
                  --critical, defaut 60,  Critical time (s)
  -w2         (Optionnel, prend 1 argument)
                  --warn2, defaut 10,  warn2 time (s), à utiliser avec '-e'
  -v          (Optionnel, prend 0 arguments)
                  verbeux
  -vv         (Optionnel, prend 0 arguments)
                  plus verbeux !
  -z          (Optionnel, prend 0 arguments)
                  --gzip, ajoute gzip,deflate aux entêtes HTTP
  -n          (Optionnel, prend 0 arguments)
                  --no-inner-links, ne télécharge pas les liens, récupère uniquement le HTML (permet de remplacer check_http)
```

  * **-c** Critical status time. Si tous ou un seul élément est plus lent que ce temps, un status critique sera retourné.
  * **-e** Mode étendu - utilise le statut 'unknow' comme un deuxième niveau de warning. A utiliser en connaissance de cause ...

Ne pas configurer le mode verbeux dans nagios ...

Pour utiliser dans nagios:

```
define command{
  command_name  check-webpage
  command_line  $USER1$/check_webpage.rb -u $ARG1$
}
```

# Exemples #

  * usage standard
```
$ ./check_webpage.rb -u http://www.google.com
OK - 15ko, 2 files, 0.74s
```

  * Verbeux
```
$ ./check_webpage.rb -v -u http://www.google.com

 * Get main page: http://www.google.com/
   -> 302, main page is now: http://www.google.fr/
[200] OK s(6691) t(2.611563)

 * downloading inner links (1) ...
[200] OK /intl/fr_fr/images/logo.gif -> s(8866o) t(0.72s)

 * results
Inner links count: 1
Inner links dl cumulated time: 0.72s
Total time: 3.33s
Total size: 15ko

OK - 15ko, 2 files, 3.33s
```


  * Plus verbeux
```
$ ./check_webpage.rb -vv -u http://www.google.com

 * ARGS: c=60 w=5 e=0 w2=10 u=http://www.google.com

 * Get main page: http://www.google.com/
   -> 302, main page is now: http://www.google.fr/
[200] OK s(6697) t(0.251305)

 * parsing results (1) ...
http://www.google.fr/intl/fr_fr/images/logo.gif -> add

 * remove duplicated links: 1 -> 1

 * downloading inner links (1) ...
[200] OK /intl/fr_fr/images/logo.gif -> s(8866o) t(0.09s)

 * results
Inner links count: 1
Inner links dl cumulated time: 0.09s
Total time: 0.35s
Total size: 15ko

OK - 15ko, 2 files, 0.35s
```