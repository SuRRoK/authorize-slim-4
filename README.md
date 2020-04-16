# authorize-slim-4
Slim 4 Authorization Tutorial

# Installation

### Dependencies
 - Vagrant
 - VirtualBox or other available vagrant virtual machine provider


1. Clone, enter, and determine the path the application
```
https://github.com/zhorton34/authorize-slim-4.git

cd authorize-slim-4

pwd
```

2. Copy output from `pwd` command (Full path to present working directory)

3. Open `authorize-slim-4/homestead.yaml` and update folders map path (Has comment next to it below)
```
ip: 192.168.10.10
memory: 2048
cpus: 2
provider: virtualbox
authorize: ~/.ssh/id_rsa.pub
keys:
    - ~/.ssh/id_rsa
folders:
    -
       # Replace `map: /Users/zhorton/tutorials/authorize-slim-4`
       # With `map: {the_copied_output_from_pwd_in_step_2}
        map: /Users/zhorton/tutorials/authorize-slim-4
        ## map Update with path you cloned the repository on your machine
        to: /home/vagrant/code
sites:
    -
        map: slim.auth
        to: /home/vagrant/code/public
databases:
    - slim
features:
    -
        mariadb: false
    -
        ohmyzsh: false
    -
        webdriver: false
name: authorize-slim-4
hostname: authorize-slim-4
```

4. Save and close homestead.yaml

5. Run `vagrant up --provision` to boot up your virtual machine

6. Once booted, open up your localmachine's host file (sudo vim /etc/hosts on linux or mac)

7. Add slim.auth

8. Boot up the vagrant virtual
```
  vagrant up --provision
```

9. Open your localmachines `host` file (sudo vim /etc/hosts on linux and mac)

10. Add `slim.auth` and the ip defined in the homestead.yaml
 (Example below, shouldn't need to change from example)
```
##
# Host Database
#
# localhost is used to configure the loopback interface
# when the system is booting.  Do not change this entry.
##
127.0.0.1	localhost
255.255.255.255	broadcasthost
::1             localhost

###########################
# Homestead Sites (Slim 4)
###########################
192.168.10.10   slim.auth
```

11. Close and save hosts file

12. Test out `http://slim.auth/` in your browser
(Make sure vagrant has properly finished booting from step 8)

13. `cd` back into `authorize-slim-4` within your terminal

14. Run `vagrant ssh`

15. Once ssh'd into your virtual machine, run
```
cd code && cp .env.example .env && composer install && npm install
```

===

## List Slim Console Commands

1. Run `php slim` from project root

```
Console
----------------------------------------
 ~/tutorials/authorize-slim-4 php slim
-----------------------------------------


Console Tool

Usage:
  command [options] [arguments]

Options:
  -h, --help            Display this help message
  -q, --quiet           Do not output any message
  -V, --version         Display this application version
      --ansi            Force ANSI output
      --no-ansi         Disable ANSI output
  -n, --no-interaction  Do not ask any interactive question
  -v|vv|vvv, --verbose  Increase the verbosity of messages: 1 for normal output, 2 for more verbose output and 3 for debug

Available commands:
  help              Displays help for a command
  list              Lists commands
  tinker
 db
  db:fresh          Drop all database table and re-migrate, then re-seed all tables
  db:migrate        Migration migrations to database
  db:seed           Run Database Seeders
  db:show           Show all of your models/table records in a table for display
 make
  make:migration    Make or scaffolded new migration for our database
  make:seeder       Generate a database seeder scaffold
 migrate
  migrate:rollback  Rollback Previous Database Migration
 view
  view:clear        Remove Cache For View Templates
```



## Migrate and Seed Database
1. `vagrant ssh`
2. `cd code`
3. `php slim db:migrate`
4. `php slim db:seed`


## Show database table example
1. `vagrant ssh`
2. `cd code`
3. `php slim db:show users`


## Register Middleware
1. Create Middleware Class (Example: `\App\Http\Middleware\RouteGuardMiddleware::class`)
2. Open `authorize-slim-4/app/Http/HttpKernel`
3. Add \App\Http\Middleware\RouteGuardMiddleware::class to a specific route group or globally
```
class HttpKernel extends Kernel
{
    /**
     * Global Middleware
     *
     * @var array
     */
    public array $middleware = [
//        Middleware\ExampleAfterMiddleware::class,
//        Middleware\ExampleBeforeMiddleware::class
    ];

    /**
     * Route Group Middleware
     */
    public array $middlewareGroups = [
        'api' => [],
        'web' => [
            Middleware\RouteContextMiddleware::class,
            'csrf'
        ]
    ];
}
```

## Create and Register new `php slim` command
1. Add new ExampleCommand.php File and class at app/console/commands/ExampleCommand.php
2. Define Command name, description, arguments, and handler within class
```
class ExampleCommand extends Command
{
    protected $name = 'example:command';
    protected $help = 'Example Command For Readme';
    protected $description = 'Example Command For Readme';

    public function arguments()
    {
        return [
            'hello' => $this->required('Description for this required command argument'),
            'world' => $this-optional('Description for this optional command argument', 'default')
        ];
    }

    public function handler()
    {
        /** Collect Console Input **/
        $all_arguments = $this->input->getArguments();
        $optional_argument = $this-input->getArgument('world');
        $required_argument = $this->input->getArgument('hello');

        /** Write Console Output **/
        $this->warning("warning output format");
        $this->info("Success output format");
        $this->comment("Comment output format");
        $this->error("Uh oh output format");
    }
}
```
3. Open app\console\ConsoleKernel.php
4. Add ExampleCommand::class to Registered Commands
```
<?php

namespace App\Console;

use Boot\Foundation\ConsoleKernel as Kernel;

class ConsoleKernel extends Kernel
{
    public array $commands = [
        Commands\ExampleCommand::class, // Registered example command
        Commands\ViewClearCommand::class,
        Commands\MakeSeederCommand::class,
        Commands\DatabaseRunSeeders::class,
        Commands\DatabaseFreshCommand::class,
        Commands\MakeMigrationCommand::class,
        Commands\DatabaseMigrateCommand::class,
        Commands\DatabaseTableDisplayCommand::class,
        Commands\DatabaseRollbackMigrationCommand::class
    ];
}

```
