Tracery for Python
==================

.. image:: https://img.shields.io/travis/aparrish/pytracery.svg
        :target: https://travis-ci.org/aparrish/pytracery

This is a (rough) port of `Kate Compton <http://www.galaxykate.com/>`_'s
wonderful `Tracery <http://tracery.io/>`_ to Python. The port
is by `Allison Parrish <http://www.decontextualize.com/>`_.

I'm always writing little one-off recursive template/grammar expansion
functions in my Python generative text projects. And I *love* working with
Tracery! So I figured: why not save myself (and potentially others) some time
and just make it possible to use Tracery from within Python? This port is the
result!

The port is a fairly literal Python translation of `this version
<https://github.com/galaxykate/tracery/blob/8baa6ec53271ce7526e14b0ae3069a7469c6f035/js/tracery/tracery.js>`_
of `tracery.js` in the official repository's `tracery2` branch.

Installation
------------

Install with pip like so::

    pip install tracery

You can also download the source code and install manually::

    python setup.py install

Usage
-----

See `Kate Compton's Tracery
tutorial <http://www.crystalcodepalace.com/traceryTut.html>`_ for information
about how Tracery works. In the Python port, you use Python dictionaries
instead of JavaScript objects for the rules, but the concept is the same
otherwise.

The base modifiers have been extended to include modifiers based on the
`inflect.py <https://github.com/pwdyson/inflect.py>`_ package.

::

    import tracery
    from tracery.modifiers import base_english

    rules = {
        'origin': '#hello.capitalize#, #location.plural(1)#! #num.number_to_words(decimal=mark,andword=annnnd)#',
        'hello': ['hello', 'greetings', 'howdy', 'hey'],
        'location': ['world', 'solar system', 'galaxy', 'universe'],
        'num': ['1023.5','12.12', '203']
    }

    grammar = tracery.Grammar(rules)
    grammar.add_modifiers(base_english)
    print grammar.flatten("#origin#") # prints, e.g., "Hello, world!"

Any valid Tracery grammar should work in this port. The ``base_english``
modifiers in ``tracery.modifiers`` are a port of the modifiers in the JavaScript
package. Many aspects of Tracery are not standardized, so in some edge cases
you may get output that doesn't exactly conform to what you would get if you
used the same grammar with the JavaScript version. (e.g., "None" in strings
where in JavaScript you might see "undefined")

Command line
------------

You can run the module on a JSON Tracery grammar file on the command line to output example lines::

    positional arguments:
        json        Input JSON file
        number      Number of lines to generate (default: 1)

For example::

    $ python -m tracery potterpapers.json
    Harry Potter and the Tchebyshev transforms of the first and second kind

    $ python -m tracery potterpapers.json 3
    Harry Potter and the Impact of Extremes in Outdoor Temperature and Sunshine Exposure on Birth Weight.
    Harry Potter and the Chromatin Remodelling Enzymes SNF2H and SNF2L Position Nucleosomes adjacent to CTCF and Other Transcription Factors.
    Harry Potter and the Model Organism Hermissenda crassicornis (Gastropoda: Heterobranchia) Is a Species Complex.

Generating Sentences from *pandas* `Dataframe` rows
---------------------------------------------------

::
    import pandas as pd

    df=pd.DataFrame({'name':['Jo','Sam'], 'pos':[1,2]})

    rules = {'origin':"#name# was placed #posord.number_to_words#.",
             'posord':'#pos.ordinal#'}

    def row_mapper(row):
        row=row.to_dict()
        for k in row:
            rules[k] = str(row[k])

        grammar = tracery.Grammar(rules)
        grammar.add_modifiers(base_english)
        return grammar.flatten("#origin#")

    df['report']=df.apply(lambda row: row_mapper(row), axis=1)

License
-------

This port inherits Tracery's original Apache License 2.0.

::

    Copyright 2016 Allison Parrish
    Based on code by Kate Compton

    Licensed under the Apache License, Version 2.0 (the "License");
    you may not use this file except in compliance with the License.
    You may obtain a copy of the License at

        http://www.apache.org/licenses/LICENSE-2.0

    Unless required by applicable law or agreed to in writing, software
    distributed under the License is distributed on an "AS IS" BASIS,
    WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
    See the License for the specific language governing permissions and
    limitations under the License.
