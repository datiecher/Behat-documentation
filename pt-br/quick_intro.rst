Introdução Rápida ao Behat
==========================

Bem-vindo ao Behat! Behat é uma ferramenta que possibilita o uso de
`behavior driven development`_ (BDD). Com o BDD, você escreve  histórias, que
podem ser lidas por pessoas, e descrevem o comportamento de sua aplicação. Estas
histórias podem então serem executadas como testes para sua aplicação. E sim, é
tão legal quanto parece!

Por exemplo, imagine que você foi contratato para cirar o famoso comando UNIX
``ls``. Um contratanete poderia dizer para você:

.. code-block:: gherkin

    Funcionalidade: ls
      A fim de visualizar a estrutura de diretórios
      Como um usuário UNIX
      Preciso ser capaz de listar o conteúdo do diretório atual

      Cenário: Listar dois arquivos em um diretório
          Dado que eu esteja no diretório "teste"
             E eu possua um arquivo chamado "foo"
             E eu possua um arquivo chamado "bar"
        Quando eu executar "ls"
         Então deve aparecer:
           """
           bar
           foo
           """

Neste tutorial, mostraremos como o Behat pode executar esta simples história
como um teste que verifica que o comando ``ls`` funciona como descrito.

É isso! O Behat pode ser usado para testar qualquer coisa, inclusive comportamento
relacionado à web, através da biblioteca `Mink`_.

.. note::

    Se você quer aprender mais sobre a filosofia de testar o "comportamento"
    de sua aplicação, leia `Qual o conteúdo de uma História?`_

.. note::

    O Behat foi inspirado pelo projeto para Ruby chamado `Cucumber`_.

Instalação
----------

O Behat é um executável que você irá rodar através da linha de comando para
executar suas histórias como testes. Antes de começar, assegure-se de que você
possui ao menos o PHP 5.3.1 instalado.

Método #1 (PEAR)
~~~~~~~~~~~~~~~~

A maneira mais fácil de instalar o Behat é através do PEAR:

.. code-block:: bash

    $ pear channel-discover pear.symfony.com
    $ pear channel-discover pear.behat.org
    $ pear install behat/behat

Você pode agora executar o Behat simplesmente usando o comando ``behat``.

.. code-block:: bash

    $ behat

Método #2 (PHAR)
~~~~~~~~~~~~~~~~

Você pode usar também o pacote phar do Behat:

.. code-block:: bash

    $ wget https://github.com/downloads/Behat/Behat/behat.phar

Você pode agora executar o Behat simplesmente rodando o arquivo phar pelo ``php``:

.. code-block:: bash

    $ php behat.phar

Método #3 (Git)
~~~~~~~~~~~~~~~

Você pode também clonar o projeto com o Git executando:

.. code-block:: bash

    $ git clone git://github.com/Behat/Behat.git && cd Behat
    $ git submodule update --init

Depois de finalizado o download, você pode executar o behat digitando:

.. code-block:: bash

    $ ./caminho/do/diretório/bin/do/Behat/behat.php

Basic Usage
-----------

In this example, we'll rewind several decades and pretend we're building
the original UNIX ``ls`` command. Create a new directory and setup behat
inside that directory:

.. code-block:: bash

    $ mkdir ls_project
    $ cd ls_project
    $ behat --init

The ``behat --init`` will create a ``features/`` directory with some basic
things to get your started.

Define your Feature
~~~~~~~~~~~~~~~~~~~

Everything in Behat always starts with a *feature* that you want to describe
and then implement. In this example, the feature will be the ``ls`` command,
which can be thought of as one feature of the whole UNIX system. Since the
feature is the ``ls`` command, start by creating a ``features/ls.feature``
file:

.. code-block:: gherkin

    # features/ls.feature
    Feature: ls
      In order to see the directory structure
      As a UNIX user
      I need to be able to list the current directory's contents

Every feature starts with this same format: a line naming the feature, followed
by three lines that describe the benefit, the role and the feature itself.
And while this section is required, its contents aren't actually important
to Behat or your eventual test. This section is important, however, so that
each feature is described consistently and is readable by other people.

Define a Scenario
~~~~~~~~~~~~~~~~~

Next, add the following scenario to the end of the ``features/ls.feature``
file:

.. code-block:: gherkin

    Scenario: List 2 files in a directory
      Given I am in a directory "test"
      And I have a file named "foo"
      And I have a file named "bar"
      When I run "ls"
      Then I should get:
        """
        bar
        foo
        """

.. tip::

    The special ``"""`` syntax seen on the last few lines is just a special
    syntax for defining steps on multiple lines. Don't worry about it too
    much for now.

Each feature is defined by one or more "scenarios", which explain how that
feature should act under different conditions. This is the part that will
be transformed into a test. Each scenario always follows the same basic format:

.. code-block:: gherkin

    Scenario: Some description of the scenario
      Given [some context]
      When [some event]
      Then [outcome]

Each part of the scenario - the *context*, the *event*,  and the *outcome* -
can be extended by adding the ``And`` or ``But`` keyword:

.. code-block:: gherkin

    Scenario: Some description of the scenario
      Given [some context]
        And [more context]
       When [some event]
        And [second event occurs]
       Then [outcome]
        And [another outcome]
        But [another outcome]

There's no actual difference between, ``Then``, ``And`` ``But`` or any of
the other words that start each line. These keywords are all made available
so that your scenarios are natural and readable.

Executing Behat
~~~~~~~~~~~~~~~

You've now defined the feature and one scenario for that feature. You're
ready to see Behat in action! Try executing Behat from inside your ``ls_project``
directory:

.. code-block:: bash

    $ behat

If everything worked correctly, you should see something like this:

.. image:: /images/ls_no_defined_steps.png
   :align: center

Writing your Step definitions
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Behat automatically finds the ``features/ls.feature`` file and tries to execute
its ``Scenario`` as a test. However, we haven't told Behat what to do with
statements like ``Given I am in a directory "test"``, which causes an error.
Behat works by matching each statement of a ``Scenario`` to a list of regular
expression "steps" that you define. In other words, it's your job to tell
Behat what to do when it sees ``Given I am in a directory "test"``. Fortunately,
Behat helps you out by printing the regular expression that you probably
need in order to create that step definition:

.. code-block:: text

    You can implement step definitions for undefined steps with these snippets:

        /**
         * @Given /^I am in a directory "([^"]*)"$/
         */
        public function iAmInADirectory($argument1)
        {
            throw new PendingException();
        }

Let's use Behat's advice and add the following to the ``features/bootstrap/FeatureContext.php``
file, renaming ``$argument1`` to ``$dir``, simply for clarity:

.. code-block:: php

    <?php # features/bootstrap/FeatureContext.php

    use Behat\Behat\Context\BehatContext,
        Behat\Behat\Exception\PendingException;
    use Behat\Gherkin\Node\PyStringNode,
        Behat\Gherkin\Node\TableNode;

    class FeatureContext extends BehatContext
    {
        /**
         * @Given /^I am in a directory "([^"]*)"$/
         */
        public function iAmInADirectory($dir)
        {
            if (!file_exists($dir)) {
                mkdir($dir);
            }
            chdir($dir);
        }
    }

Basically, we've started with the regular expression suggested by Behat, which
makes the value inside the quotations (e.g. "test") available as the ``$dir``
variable. Inside the method, we simple create the directory and move into it.

Repeat this for the other three missing steps so that your ``FeatureContext.php``
file looks like this:

.. code-block:: php

    <?php # features/bootstrap/FeatureContext.php

    use Behat\Behat\Context\BehatContext,
        Behat\Behat\Exception\PendingException;
    use Behat\Gherkin\Node\PyStringNode,
        Behat\Gherkin\Node\TableNode;

    class FeatureContext extends BehatContext
    {
        private $output;

        /** @Given /^I am in a directory "([^"]*)"$/ */
        public function iAmInADirectory($dir)
        {
            if (!file_exists($dir)) {
                mkdir($dir);
            }
            chdir($dir);
        }

        /** @Given /^I have a file named "([^"]*)"$/ */
        public function iHaveAFileNamed($file)
        {
            touch($file);
        }

        /** @When /^I run "([^"]*)"$/ */
        public function iRun($command)
        {
            exec($command, $output);
            $this->output = trim(implode("\n", $output));
        }

        /** @Then /^I should get:$/ */
        public function iShouldGet(PyStringNode $string)
        {
            if ((string) $string !== $this->output) {
                throw new Exception(
                    "Actual output is:\n" . $this->output
                );
            }
        }
    }

.. note::

    When you specify multi-line step arguments - like we did using the triple
    quotation syntax (``"""``) in the above scenario, the value passed into
    the step function (e.g. ``$string``) is actually an object, which can
    be converted into a string using ``(string) $string`` or
    ``$string->getRaw()``.

Great! Now that you've defined all of your steps, run Behat again:

.. code-block:: bash

    $ behat

.. image:: /images/ls_passing_one_step.png
   :align: center

Success! Behat executed each of your steps - creating a new directory with
two files and running the ``ls`` command - and compared the result to the
expected result.

Of course, now that you've defined your basic steps, adding more scenarios
is easy. For example, add the following to your ``features/ls.feature`` file
so that you now have two scenarios defined:

.. code-block:: gherkin

    Scenario: List 2 files in a directory with the -a option
      Given I am in a directory "test"
      And I have a file named "foo"
      And I have a file named ".bar"
      When I run "ls -a"
      Then I should get:
        """
        .
        ..
        .bar
        foo
        """

Run Behat again. This time, it'll run two tests, and both will pass.

.. image:: /images/ls_passing_two_steps.png
   :align: center

That's it! Now that you've got a few steps defined, you can probably dream
up lots of different scenarios to write for the ``ls`` command. Of course,
this same basic idea could be used to test web applications, and Behat integrates
beautifully with a library called `Mink`_ to do just that.

Of course, there's still lot's more to learn, including more about the
:doc:`Gherkin syntax </guides/1.gherkin>` (the language used in the ``ls.feature``
file).

Some more Behat Basics
----------------------

When you run ``behat --init``, it sets up a directory that looks like this:

.. code-block:: bash

    |-- features
       `-- bootstrap
           `-- FeatureContext.php

Everything related to Behat will live inside the ``features`` directory, which
is composed of three basic areas:

1. ``features/`` - Behat looks for ``*.feature`` files here to execute

2. ``features/bootstrap/`` - Every ``*.php`` file in that directory will
   be autoloaded by Behat before any actual steps are executed

3. ``features/bootstrap/FeatureContext.php`` - This file is the context
   class in which every scenario step will be executed

More about Features
-------------------

As you've already seen, a feature is a simple, readable plain text file,
in a format called Gherkin. Each feature file follows a few basic rules:

1. Every ``*.feature`` file conventionally consists of a single "feature"
   (like the ``ls`` command or *user registration*).

2. A line starting with the keyword ``Feature:`` followed by its title and
   three indented lines defines the start of a new feature.

3. A feature usually contains a list of scenarios. You can write whatever
   you want up until the first scenario: this text will become the feature
   description.

4. Each scenario starts with the ``Scenario:`` keyword followed by a short
   description of the scenario. Under each scenario is a list of steps, which
   must start with one of the following keywords: ``Given``, ``When``, ``Then``,
   ``But`` or ``And``. Behat treats each of these keywords the same, but you
   should use them as intended for consistent scenarios.

.. tip::

    Behat also allows you to write your features in your native language.
    In other words, instead of writing ``Feature``, ``Scenario`` or ``Given``,
    you can use your native language by configuring Behat to use one of its
    many supported languages.
    
    To check if your language is supported and to see the available keywords,
    run:
    
    .. code-block:: bash
    
        $ behat --story-syntax --lang YOUR_LANG

    Supported languages include (but are not limited to) ``fr``, ``es``, ``it``
    and, of course, the english pirate dialect ``en-pirate``.

    Keep in mind, that any language, different from ``en`` should be explicitly
    marked with ``# language: ...`` comment at the begining of your
    ``*.feature`` file:

    .. code-block:: gherkin

        # language: fr
        Fonctionnalité: ...
          ...

You can read more about features and Gherkin language in ":doc:`/guides/1.gherkin`"
guide.

More about Steps
----------------

For each step (e.g. ``Given I am in a directory "test"``), Behat will look
for a matching step definition by matching the text of the step against the
regular expressions defined by each step definition.

A step definition is written in php and consists of a keyword, a regular
expression, and a callback. For example:

.. code-block:: php

    /**
     * @Given /^I am in a directory "([^"]*)"$/
     */
    public function iAmInADirectory($dir)
    {
        if (!file_exists($dir)) {
            mkdir($dir);
        }
        chdir($dir);
    }

A few pointers:

1. ``@Given`` is a definition keyword. There are 3 supported keywords in
   annotations: ``@Given``/``@When``/``@Then``. These three definition keywords
   are actually equivalent, but all three are available so that your step
   definition remains readable.

2. The text after the keyword is the regular expression (e.g. ``/^I am in a directory "([^"]*)"$/``).

3. All search patterns in the regular expression (e.g. ``([^"]*)``) will become
   method arguments (``$dir``).

4. If, inside a step, you need to tell Behat that some sort of "failure" has
   occurred, you should throw an exception:

    .. code-block:: php

       /**
        * @Then /^I should get:$/
        */
       public function iShouldGet(PyStringNode $string)
       {
           if ((string) $string !== $this->output) {
               throw new Exception(
                   "Actual output is:\n" . $this->output
               );
           }
       }

.. tip::

    Behat doesn't come with its own assertion tool, but you can use any proper
    assertion tool out there. Proper assertion tool is a library, which
    assertions throw exceptions on fail. For example, if you're familiar with
    PHPUnit, you can use its assertions in Behat:

    .. code-block:: php

        <?php # features/bootstrap/FeatureContext.php

        use Behat\Behat\Context\BehatContext;
        use Behat\Gherkin\Node\PyStringNode;

        require_once 'PHPUnit/Autoload.php';
        require_once 'PHPUnit/Framework/Assert/Functions.php';

        class FeatureContext extends BehatContext
        {
            /**
             * @Then /^I should get:$/
             */
            public function iShouldGet(PyStringNode $string)
            {
                assertEquals($string->toRaw(), $this->output);
            }
        }

In the same way, any step that does *not* throw an exception will be seen
by Behat as "passing". 

You can read more about step definitions in ":doc:`/guides/2.definitions`" guide.

The Context Class: ``FeatureContext``
-------------------------------------

Behat creates an context object for each scenario and executes all scenario
steps inside that same object. In other words, if you want to share variables
between steps, you can easily do that by setting property values on the context
object itself (which was shown in the previous example).

You can read more about ``FeatureContext`` in ":doc:`/guides/4.context`" guide.

The ``behat`` Command Line Tool
-------------------------------

Behat comes with a powerful console utility responsible for executing the
Behat tests. The utility comes with a wide array of options.

To see options and usage for the utility, run:

.. code-block:: bash

    $ behat -h

One of the handiest things it does it to show you all of the step definitions
that you have configured in your system. This is an easy way to recall exactly
how a step you defined earlier is worded:

.. code-block:: bash

    $ behat --definitions

You can read more about Behat CLI in ":doc:`/guides/6.cli`" guide.

What's Next?
------------

Congratulations! You now know everything you need in order to get started
with behavior driven development and Behat. From here, you can learn more
about the :doc:`Gherkin</guides/1.gherkin>` syntax or learn how to test your
web applications by using Behat with Mink.

* :doc:`/cookbook/behat_and_mink`
* :doc:`/guides/1.gherkin`
* :doc:`/guides/6.cli`

.. _`behavior driven development`: http://en.wikipedia.org/wiki/Behavior_Driven_Development
.. _`Mink`: https://github.com/behat/mink
.. _`What's in a Story?`: http://blog.dannorth.net/whats-in-a-story/
.. _`Cucumber`: http://cukes.info/
.. _`Goutte`: https://github.com/fabpot/goutte
.. _`PHPUnit`: http://phpunit.de
.. _`Testing Web Applications with Mink`: https://github.com/behat/mink
