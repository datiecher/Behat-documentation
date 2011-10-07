Documentação do Behat
=====================

Behat é um framework de código livre, baseado no PHP 5.3, para behavior driven
develolment (desenvolvimento orientado a comportamento).
Mas afinal o que é *behavior driven development*? É a ideia de você começar
a implementação escrevendo frases que possam ser entendidas por outras pessoas
a fim de descrever uma funcionalidade de sua aplicação e como ela deve funcionar.

Por exemplo, imagine que você esta prestes a criar o famoso comando UNIX ``ls``.
Antes de começar, você descreve como a funcionalidade deve funcionar:

.. code-block:: gherkin

    Funcionalidade: ls
      A fim de visualizar a estrutura de diretórios
      Como um usuário UNIX
      Preciso ser capaz de listar o conteúdo do diretório atual

      Cenário:
          Dado que eu esteja no diretório "teste"
             E eu possua um arquivo chamado "foo"
             E eu possua um arquivo chamado "bar"
        Quando eu executar "ls"
         Então deve aparecer:
           """
           bar
           foo
           """

Como um desenvolvedor, seu trabalho estará concluído assim que você fizer com
que o comando ``ls`` se comporte como descrito no *Cenário*.

Agora, não seria legal se alguma coisa pudesse ler esta frase e usá-la para
rodar um teste para o comando ``ls``? Então, é exatamente isso que o Behat faz!
Como você pode ver, o Behat é fácil de aprender, rápido de usar e ele irá devolver
a diversão aos seus testes.

.. note::

    O Behat foi inspirado pelo projeto para Ruby chamado Cucumber, especialmente
    sua sintaxe (chamada de Gherkin).

Breve Introdução
----------------

Para se tornar um *Behat'er* em 20 minutos, basta mergulhar no guia de introdução
rápida e aproveitar!

.. toctree::
    :maxdepth: 2

    quick_intro

Guides
------

Learn Behat with the topical guides:

.. toctree::
    :maxdepth: 1

    guides/1.gherkin
    guides/2.definitions
    guides/3.hooks
    guides/4.context
    guides/5.closures
    guides/6.cli
    guides/7.config

Cookbook
--------

Learn specific solutions for specific needs:

.. toctree::
    :maxdepth: 1

    cookbook/behat_and_mink
    cookbook/bdd_in_symfony2_with_behat_mink_and_zombiejs
    cookbook/using_the_profiler_with_minkbundle
    cookbook/intercepting_the_redirections
    cookbook/migrate_from_1x_to_20

Frameworks Integration
----------------------

Behat can be used with any framework out there without the need in
additional configuration, but to make your life even more fun, it
provides plugins for some major frameworks out there:

.. toctree::
    :maxdepth: 1

    bundle/index

Behat API
---------

Read libraries API:

* `Behat API <http://docs.behat.org/behat-api/>`_ - Behat code API
* `Gherkin API <http://docs.behat.org/gherkin-api/>`_ - Gherkin parser API

More about Behavior Driven Development
--------------------------------------

Once you're up and running with Behat, you can learn more about behavioral
driven development via the following links. Though both tutorials are specific
to Cucumber, Behat shares a lot with Cucumber and the philosophies are one
and the same.

* `Dan North's "What's in a Story?" <http://dannorth.net/whats-in-a-story/>`_
* `Cucumber's "Backgrounder" <https://github.com/cucumber/cucumber/wiki/Cucumber-Backgrounder>`_
