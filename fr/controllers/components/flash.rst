Flash
#####

.. php:namespace:: Cake\Controller\Component

.. php:class:: FlashComponent(ComponentCollection $collection, array $config = [])

FlashComponent est un moyen de définir des messages de notifications à afficher
après avoir traité les données envoyées via un formulaire ou acquitter des informations.
CakePHP appelle ces messages des "messages flash". FlashComponent écrit les messages flash
dans ``$_SESSION`` pour être affichés dans une View en utilisant
:doc:`FlashHelper </views/helpers/flash>`.

Définir les Messages Flash
==========================

FlashComponent fournit deux façons de définir des messages flash : sa méthode
magique ``__call()`` et sa méthode ``set()``. Pour remplir votre application
sémantiquement, la méthode magique ``__call()`` de FlashComponent vous permet
d'utiliser un nom de méthode qui est lié à un element qui se trouve dans le
répertoire **templates/element/flash**. Par convention, les méthodes en
camelcase vont être liées à un nom d'élément en minuscule et avec des
underscores (_)::

    // Utilise templates/element/flash/success.php
    $this->Flash->success('C\'était un succès');

    // Utilise templates/element/flash/great_success.php
    $this->Flash->greatSuccess('C\'était un grand succès');

De façon alternative, pour définir un message sans rendre un element,
vous pouvez utiliser la méthode ``set()``::

    $this->Flash->set('Ceci est un message');

Les messages Flash sont ajoutés à un tableau en interne. Les appels successifs à
``set()`` ou ``__call()`` avec la même clé ajoutera les messages dans
``$_SESSION``. Si vous souhaitez écraser les messages existants lors de la définition
d'un flash message, mettez l'option ``clear`` sur ```true`` lors de la configuration
du composant.

Les méthodes ``__call()`` et ``set()`` de FlashComponent prennent de façon
optionnelle un deuxième paramètre, un tableau d'options:

* ``key`` Par défaut à 'flash'. La clé du tableau trouvé sous la clé 'Flash'
  dans la session.
* ``element`` Par défaut à null, mais il va automatiquement être défini lors de
  l'utilisation de la méthode magique ``__call()``. Le nom d'élément à utiliser
  pour le rendu.
* ``params`` Un tableau en option de clés/valeurs pour rendre disponible des
  variables dans un element.
* ``clear`` attend un ``bool`` et permet d'effacer tous les messages de la pile
  courante et d'en démarrer une nouvelle.

Un exemple de l'utilisation de ces options::

    // Dans votre Controller
    $this->Flash->success("L'utilisateur a été sauvegardé", [
        'key' => 'positive',
        'params' => [
            'name' => $user->name,
            'email' => $user->email
        ]
    ]);

    // Dans votre Vue
    <?= $this->Flash->render('positive') ?>

    <!-- Dans templates/element/flash/success.php -->
    <div id="flash-<?= h($key) ?>" class="message-info success">
        <?= h($message) ?>: <?= h($params['name']) ?>, <?= h($params['email']) ?>.
    </div>

Notez que le paramètre ``element`` sera toujours surchargé en utilisant
``__call()``. Afin de récupérer un element spécifique d'un plugin, vous
devez définir le paramètre ``plugin``.
Par exemple::

    // Dans votre Controller
    $this->Flash->warning('My message', ['plugin' => 'PluginName']);

Le code ci-dessus va utiliser l'element **warning.php** dans
**plugins/PluginName/templates/element/flash** pour afficher le message
flash.

.. note::

    Par défaut, CakePHP échappe le contenu dans les messages flash pour des
    raisons de sécurité. Si vous utilisez une requête ou des données
    d'utilisateur dans vos messages flash, ceux-ci sont échappés et donc
    sécurisés pour l'affichage. Si vous souhaitez afficher du HTML, vous devez
    passer un paramètre ``escape`` et aussi ajuster les templates pour permettre
    la désactivation de l'échappement quand un tel paramètre est passé.

HTML dans des Messages Flash
============================

Il est possible d'afficher le HTML dans des messages flash en utilisant la clé
d'option ``'escape'``::

    $this->Flash->info(sprintf('<b>%s</b> %s', h($highlight), h($message)), ['escape' => false]);

Assure-vous de bien échapper l'input manuellement, ensuite. Dans l'exemple
ci-dessus, ``$highlight`` et ``$message`` sont des inputs non-HTML et donc sont
échappés.

Pour plus d'informations sur le rendu de vos messages flash, consultez la
section :doc:`FlashHelper </views/helpers/flash>`.
