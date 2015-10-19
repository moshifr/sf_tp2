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
