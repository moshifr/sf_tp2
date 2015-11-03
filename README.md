TP2 - From Scratch
------------------
Nous allons dans ce second TP réaliser le premier TP mais en repartant de zero. Il sera donc question de créer un blog avec un backoffice.
Le front devra être **catégorisé, paginé et lié à un utilisateur**.
Le backoffice sera **sécurisé via un formulaire de connexion** lié à une table User.

 


----------

Etapes :
--------

 1. Créer projet
 2. Créer controller front et back 
 3. Créer entitées
	 1. Post (titre, description, date_add, date_update, views)
	 2. Category (titre)
	 3. User (ne rajouter aucun champs) 
 4. Créer CRUD
 5. Pagination
 6. Menu par catégorie
 7. UserInterface
 8. Login front
	 1. Créer SecurityController
 9. Sécuriser CRUD
 10. Intégration template 
	 1. Bootstrap par exemple
	 2. Template homepage (Grosse une et 4 articles en dessous sur une seule colonne)
	 3. Template rubrique et article (Colonne contenu + sidebar) 
 11. Formulaire de recherche	 
 12. Affichage des Articles les plus lus dans sidebar
	 1.  Incrémentation de vues
	 2.  Affichage dans sidebar des 5 articles les plus vues
	 3.  Afficher des statistiques graphiques dans le back
 13. On rajoute un upload d'image pour Post 
 14. Commentaires
	 1. Créer entitée Commentaire (texte, enable, date_add)
	 2. Liéer à un article et un utilisateur
	 3. Formulaire dans l'article
	 4. Modération
 15. Les catégories peuvent être hiérarchisées, catégorie parent et catégories enfant
 16. Utiliser les validations de formulaire (via des @Assert) pour afficher des messages d'erreurs.
 17. Multilangue

On utilisera au maximum les annotations.

----------

Commandes et snippets utiles
----------------------------
- Créer un projet symfony avec symfony
> symfony new nom_projet 

- Créer une entité
> php app/console doctrine:generate:entity

- Générer les Getter/Setter des entités
> php app/console doctrine:generate:entities 

- Mis à jour de la base de données
> php app/console doctrine:schema:update --force

- Génération CRUD
> php app/console generate:doctrine:crud --entity=MonBundle:monEntity

- Pour mettre à jour une entité, on modifie la classe, on met à jour les getters / setter et on update la base de données.

- Utiliser le theme bootstrap pour les formulaires :  #app/config/config.yml
> twig:
 &nbsp;&nbsp;&nbsp;form:
 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;resources: ['bootstrap_3_layout.html.twig']
- Fonctions Twig pour récupérer le path des fichiers à partir du répertoire web
> {{ asset('monRepertoire/monFichier.css') }} 

- Concaténation Twig : "maString" ~ "monAutreString"

- Redirection dans un controller 
>  $this->redirect($this->generateUrl("homepage"));

- Fabrication liens twig : 
> {{ path('library_search', {'page': 1 , 'form':criteres.form}) }}

ou 
> {{ url('library_search', {'page': 1 , 'form':criteres.form}) }}

- Méthodes de recherches d'entités
>  $monEntite->find( xx ); // où xx est un id ; on choisit qu'un seul élément <br />
> $monEntite->findBy( array('critere_where' => valeurRecherchee), array('critere_tri' => 'desc' ), $limit, $offset ) ; // on choisit plusieurs éléments validant le tableau de recherche <br />
> $monEntite->findOneBy( array() ); // on choisit un seul élément validant le tableau de recherche
> 

- Query Builder 
> public function findByAuthorAndDate($author, $year) <br>
{ <br>
&nbsp;&nbsp;$qb = $this->createQueryBuilder('a'); <br>
&nbsp;&nbsp;$qb->where('a.author = :author') <br>
&nbsp;&nbsp;&nbsp;&nbsp;->setParameter('author', $author) <br>
&nbsp;&nbsp;&nbsp;&nbsp;->andWhere('a.date < :year') <br>
&nbsp;&nbsp;&nbsp;&nbsp;->setParameter('year', $year) <br>
&nbsp;&nbsp;&nbsp;&nbsp;->orderBy('a.date', 'DESC') <br>
&nbsp;&nbsp;; <br>
&nbsp;&nbsp;return $qb <br>
&nbsp;&nbsp;&nbsp;&nbsp;->getQuery() <br>
&nbsp;&nbsp;&nbsp;&nbsp;->getResult() <br>
&nbsp;&nbsp;; <br>
} <br>

 ou 
 
>  $query = $this->_em->createQuery('SELECT a FROM Advert a WHERE a.id = :id'); <br />
  $query->setParameter('id', $id); <br />
  return $query->getSingleResult(); <br />


- Pagination : 
>
use Doctrine\ORM\Tools\Pagination\Paginator; <br>
$dql = "SELECT p, c FROM BlogPost p"; <br>
$query = $entityManager->createQuery($dql) <br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;->setFirstResult(0) <br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;->setMaxResults(100); <br>
$paginator = new Paginator($query); <br>
$c = count($paginator); <br>
foreach ($paginator as $post) { <br>
&nbsp;&nbsp;&nbsp;echo $post->getHeadline() . "\n"; <br>
}  <br>

ou 
> // on compte le nombre d'article maximum via une query builder  dans le repository (function countArticle())
// que l'on va diviser par le nombre d'élément souhaité par page pour avoir le nombre de page pour la pagination
   $query = $this->createQueryBuilder('a')
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;->select('COUNT(a)')     
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;->getQuery();
  return $query->getSingleScalarResult();
           
           

- Relations  entités 
> /**
 \* @ORM\OneToMany(targetEntity="DeskComment")
 \*/
protected $comments;

> /**
 \* @ORM\ManyToOne(targetEntity="Desk")
 */
protected $desk;

- Formulaire simple sans classe Type
> // src/Acme/TaskBundle/Controller/DefaultController.php <br>
> namespace Acme\TaskBundle\Controller; <br>
> <br>
> use Symfony\Bundle\FrameworkBundle\Controller\Controller; <br>
> use Acme\TaskBundle\Entity\Task; <br>
> use Symfony\Component\HttpFoundation\Request; <br>
>  <br>
> class DefaultController extends Controller <br>
> { <br>
> &nbsp;&nbsp;&nbsp;public function newAction(Request $request) <br>
> &nbsp;&nbsp;&nbsp;{ <br>
> &nbsp;&nbsp;&nbsp;&nbsp;$task = $this->createForm(new Task()); <br>
> &nbsp;&nbsp;&nbsp;&nbsp;$task->setTask('Mon  titre'); <br>
> &nbsp;&nbsp;&nbsp;&nbsp;$task->seteDate(new \DateTime()); <br>
> <br>
> &nbsp;&nbsp;&nbsp;&nbsp;$form = $this->createFormBuilder($task) <br>
> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;->add('task', 'text') <br>
> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;->add('date', 'date') <br>
> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;->getForm(); <br>
> <br>
> &nbsp;&nbsp;return $this->render('AcmeTaskBundle:Default:new.html.twig', array( <br>
> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;'form' => $form->createView() <br>
> &nbsp;&nbsp;)); <br>
> <br>
> &nbsp;&nbsp;} <br>
>}

- Pour styliser un formulaire vous pouvez utiliser la méthode twig form_theme

Sécurité :

- loginAction en ayant les inputs name à username et password 
> public function loginAction() <br>
> { <br>
>&nbsp;&nbsp;$helper = $this->get('security.authentication_utils'); <br>
> <br>
> &nbsp;&nbsp;&nbsp;return $this->render('AcmeSecurityBundle:Security:login.html.twig', array( <br>
  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;'last_username' => $helper->getLastUsername(), <br>
  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;'error'         => $helper->getLastAuthenticationError(), <br>
  &nbsp;&nbsp;)); <br>
>}

- SecurityController 
> namespace ... <br>
use Symfony\Bundle\FrameworkBundle\Controller\Controller; <br>
use Symfony\Component\Security\Core\SecurityContext; <br>
class SecurityController extends Controller <br>
{ ... }  <br>

- Routing.yml
> login:
&nbsp;&nbsp;&nbsp;pattern: /login <br>
&nbsp;&nbsp;&nbsp;defaults: { _controller: AcmeSecurityBundle:Security:login } <br>
> <br>
login_check: <br>
&nbsp;&nbsp;&nbsp;pattern: /login_check <br>

- Security.yml 
> security: <br>
 &nbsp;&nbsp;&nbsp;firewalls: <br>
 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;secured_area: <br>
 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;pattern:    ^/ <br>
 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;anonymous: ~ <br>
 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;form_login:  ~  <br>
 &nbsp;&nbsp;&nbsp;access_control: <br>
 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;- { path: ^/admin, roles: ROLE_ADMIN }

UserInterface : 
http://symfony.com/doc/current/cookbook/security/custom_provider.html

UserLogin : 
1. http://symfony.com/doc/current/cookbook/security/entity_provider.html#security-crete-user-entity
2. http://symfony.com/doc/current/cookbook/security/form_login_setup.html
3. http://symfony.com/doc/current/cookbook/security/form_login.html (optionnel)



----------

Corrections :
--------

 1.  
- Installer Symfony http://symfony.com/download
- En ligne de commande se place dans le répertoire web racine (htdocs, www, ...)  
- Exécuter la commande php symfony new nom_du_repertoire_du_projet   
- Vous aurez alors un répertoire nom_du_repertoire_du_projet/ avec tous les éléments pour commencer un site sous symfony 
- J'utiliserais dans la suite du corrigé le répertoire courant : nom_du_repertoire_du_projet/.
 2. 
- Nous aurons besoin au minimum d'un controller front et back (que nous remplaceront après) , ces controllers vont nous permettre d'approcher un site avec un backoffice BackController permettant l'administration du site
- Il suffit de copier DefaultController dans le répertoire (nom_du_repertoire_du_projet/)src/Controller et de le nommer BackController et un autre fichier FrontController
- Pensez à modifier le nom des classes dans les fichiers et les noms des routes sinon ils entreront en conflit, des routes ne pouvant avoir 2 noms identiques
 3. 
- Créer entitées, pour créer des entités il faudra se positioner en ligne de commande sur le répertoire courant (nom_du_repertoire_du_projet/)
- Exécuter la commande : php app/console doctrine:generate:entity
  - -A "The entitty shortcut name : " Renseigner le nom de l'entité précédé du nom du bundle suivi de 2 points : exemple AppBundle:User, ce sera la façon de nommer les entités, une entité pouvant être créé dans plusieurs bundles, ils peuvent avoir donc le même nom d'un bundle à l'autre.
  - -Laissez en annotation
  - -A "New field name" Saisir le nom de la propriété souhaitée par exemple  title
  - -A "Field type" Mettez le type souhaité dans les types possibles : string, array, float, text, datetime ...
  - -Selon le type on vous demandera la largeur du champs de caratcère (string par exemple)
  - -Ensuite on repart sur "New field name" ... Continuez à saisir les propriétés de vos entités (les noms des champs de la table correspondante dans la base de données)
  - -Une fois que vous avez tout saisi, vous retombez sur "New Field name" pour en sortir il suffit de taper [entrée]
  - -On vous demande si vous voulez générer le repository, mettez Yes
  - -Voulez vous confirmer ? Yes
- Voila l'entité est créé et présente dans src/AppBundle/Entity
- Une entité va être votre classe PHP liée à une table dans la base de données, seulement ici nous avons seulement créé le fichier php et nom la table correspondance,
- Pour lier cette entité à une table dans la base de données il suffit de saisir cette commande : 
- php app/console doctrine:schema:update --force 
- Ensuite si tout se passe bien vous aurez dans votre phpmyadmin la table correspondance avec les champs souhaités.
- S'il y a une erreur de connexion il faudra aller dans app/config/parameters.yml qui est un fichier contenant les accès à votre base de données, il est évident que c'est un fichier sensible, ne pas l'inclure dans vos exports
- Les fichiers yml utilisent les espaces comme moyen de structurer les données, un peu comme du JSON avec les {} et les xml avec les balises, il faudra espacer les éléments enfants pour qu'ils soient inclus dedans, ne pas utiliser de tabulation, seulement des espaces, je vous invite à vous documenter sur le YAML
- Créer les 3 entités avec les champs souhaités (Post, Category, User)
 4. 
- Pour créer les CRUD (backoffice de gestion) vous avez 2 solutions, soit le faire manuellement en créant les fonctions dans BackController et de créer toutes les vues (liste, ajout, suppression, modification), soit d'utiliser une ligne de commande qui va tout vous générer : 
- **php app/console doctrine:generate:crud**
- -A "the Entity shortcut name" Saisir le nommage de votre entité : NomBundle:NomEntité (AppBundle:Post)
- -A "Do you want to generate the write actions" Yes (ceci va générer les actions et les vues concernant les méthodes d'ajout et de modification
- -Laissez en annotation
- -A"Route prefix" : saisir ce que vous voulez ce sera l'url d'appel de votre CRUD pour l'entité souhaité (par exemple /admin/post) 
- -Confirmez
- Ensuite vos controller, vues et formulaire sont générés dans votre bundle :
- src/AppBundle/Controller, src/AppBundle/Entity, src/AppBundle/Form, src/AppBundle/Resources/views
- Via le CRUD générez plusieurs entrées dans Post, on pourra ainsi travailler sur la pagination.
 5. 
- Dans DefaultController ou FrontController selon votre projet, modifiez IndexAction, vous pouvez récupérer l'IndexAction   du premier TP pour réaliser la pagination
 6. 
- Idem pour menu par catégorie
 
