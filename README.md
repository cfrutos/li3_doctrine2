li3\_doctrine2 offers integration between [the most RAD PHP framework] [lithium]
and possibly the best PHP 5.3 ORM out there: [Doctrine2] [doctrine2]

# License #

li3\_doctrine2 is released under the [BSD License] [license].

# Installation #

It is recommended that you install li3\_doctrine2 as a GIT submodule, in order
to keep up with the latest upgrades. To do so, switch to the core directory
holding your lithium application, and do:

```bash
$ git submodule add https://github.com/mariano/li3_doctrine2.git app/libraries/li3_doctrine2
$ cd app/libraries/li3_doctrine2 && git submodule update --init
$ cd _source/doctrine2 && git submodule update --init
```

# Usage #

## Adding the li3_doctrine2 library ##

Once you have downloaded li3_doctrine2 and placed it in your `app/libraries`,
or your main `libraries` folder, you need to enable it by placing the following
at the end of your `app/config/bootstrap/libraries.php` file:

```php
Libraries::add('li3_doctrine2');
```

## Defining a connection ##

Setting up a connection with

## Creating models ##

When looking to create your doctrine models, you have two choices: you can
have them follow your custom class hierarchy (or not at all), or you could
have them extend from the `BaseEntity` class provided by this library. The
advantage of choosing the later is that your models will have lithium
validation support, and are better integrated with the custom adapters (such
as for session management, or for authorization) also provided by this
library.

# Integrating Doctrine libraries #

In this section I'll cover some of the doctrine extension libraries out there,
and how to integrate them with li3_doctrine2.

## DoctrineExtensions ##

If there is one tool I would recommend you checkout for your Doctrine models,
that would be [DoctrineExtensions] [DoctrineExtensions]. It provides with a set
of behavioral extensions to the Doctrine core that will simplify your
development.

To use DoctrineExtensions, you should first add it as GIT submodule. To do so, 
switch to the core directory holding your lithium application, and do:

```bash
$ git submodule add https://github.com/l3pp4rd/DoctrineExtensions.git app/libraries/_source/DoctrineExtensions
```

Next you would use your connection configuration (in `app/config/connections.php`)
to configure Doctrine with your desired behaviors. For example, if you wish
to use Timestampable and Sluggable, you would first add the library in
`app/config/libraries.php`:

```php
Libraries::add('Gedmo', array(
    'path' => LITHIUM_APP_PATH . '/libraries/_source/DoctrineExtensions/lib/Gedmo'
));
```

And then you would use the `createEntityManager` connection option in the
connection defied in `app/config/connections.php` to implement the callable that 
enables the behaviors:

```php
Connections::add('default', array(
    'type' => 'Doctrine',
    'driver' => 'pdo_mysql',
    'host' => 'localhost',
    'user' => 'root',
    'password' => 'password',
    'dbname' => 'my_db',
    'createEntityManager' => function(array $params) {
        $params['eventManager']->addEventSubscriber(new \Gedmo\Timestampable\TimestampableListener());
        $params['eventManager']->addEventSubscriber(new \Gedmo\Sluggable\SluggableListener());

        return \Doctrine\ORM\EntityManager::create(
            $params['connection'],
            $params['configuration'],
            $params['eventManager']
        );
    }
));
```

[lithium]: http://lithify.me
[doctrine2]: http://www.doctrine-project.org
[license]: http://www.opensource.org/licenses/bsd-license.php
[DoctrineExtensions]: https://github.com/l3pp4rd/DoctrineExtensions
