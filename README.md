To-do
=====

1. Create Repositories for common tasks.

Usage 
=====

Imagine you are in a Controller:

    class DemoController extends Controller
    {
        /**
         * @Route("/hello/{name}", name="_demo_hello")
         * @Template()
         */
        public function helloAction($name)
        {
            // retrive current logged in user:
            $user = $this->get('security.context')->getToken()->getUser();
            
            // retrive user information from repository:
            $userRepo = $this->getDoctrine()->getRepository('HypebeastWordpressBundle:User');
            $userRepo->find(2);

            return array('username' => $user->getUsername());
        }

        // ...
    }

Installation
============

1. Make sure Wordpress's cookies is accessable from your Symfony 2 application. To confirm this, open up Symfony's profiler and look for `wordpress_test_cookie` inside the request tab.
   If you coudn't find the test cookie in request tab, please try to redefine the COOKIEPATH of Wordpress by editing `wp-config.php`. 
   For more information, please read http://codex.wordpress.org/Editing_wp-config.php

        // wordpress/wp-config.php

        define('COOKIEPATH', '/' );    

2. Register the namespace `Hypebeast` to your project's autoloader bootstrap script:

        // app/autoload.php

        $loader->registerNamespaces(array(
              // ...
              'Hypebeast'    => __DIR__.'/../vendor/bundles',
              // ...
        ));

3. Add this bundle to your application's kernel:

        // app/AppKernel.php

        public function registerBundles()
        {
            return array(
                // ...
                new Hypebeast\WordpressBundle\HypebeastWordpressBundle(),
                // ...
            );
        }

4. Configure the Wordpress service in your YAML configuration.
        
        # app/config/config.yml
        
        hypebeast_wordpress:
            wordpress_path: /path/to/your/wordpress

5. Add Wordpress Factories, Firewalls and User Provider to your `security.yml`. Wordpress authentication is a stateless authentication, no login cookie should be ever created by Symfony2.

        # app/config/security.yml
        
        security:
            
            # ...

            providers:
                wordpress:
                    entity: { class: Hypebeast\WordpressBundle\Entity\User, property: username }
            
            factories:
                - "%kernel.root_dir%/../vendor/bundles/Hypebeast/WordpressBundle/Resources/config/security_factories.xml"
            
            firewalls:
                secured_area:
                    pattern:    ^/demo/secured/
                    wordpress:  true
                    stateless:  true
                    # anonymous:  ~
                    
                # ...