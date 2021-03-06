*dict_voikko*
=============

PostgreSQL full text search dictionary extension utilizing the Finnish dictionary Voikko.

*dict_voikko* uses the base form of Finnish words as lexems. For compound words it uses the base form of the non-compound words from which the compound word is built. If *dict_voikko* doesn't recognise the it returns **NULL**, so *dict_voikko* should always be chained with another dictionary.

Dependencies
------------

The dictionary needs [libvoikki](http://voikko.puimula.org/) and its dependencies. A suomi-malaga dictionary with morphological analysis for Voikko is neede (e.g. dict-morpho from [http://www.puimula.org/htp/testing/voikko-snapshot/](http://www.puimula.org/htp/testing/voikko-snapshot/)) and at the moment it needs to be called **mor-morpho**.

Installation
------------

Put **dict_voikko** in **[POSTGRES]/contrib/** and compile.

### Compilation and install on Debian linux
```bash
apt-get install voikko-fi
apt-get install libvoikko-dev
make USE_PGXS=1
make install
wget http://www.puimula.org/htp/testing/voikko-snapshot/dict-morpho.zip
unzip dict-morpho.zip
mv mor-morpho/ /usr/lib/voikko/2/
```

Note: libvoikko versions 4.0+ require different morpho-files, from [http://www.puimula.org/htp/testing/voikko-snapshot-v5/](http://www.puimula.org/htp/testing/voikko-snapshot-v5/). Those files should be put into ```/usr/lib/voikko/5/```, instead of ```/usr/lib/voikko/2/```

###In PostgreSQL

Run something like:

```sql
CREATE EXTENSION dict_voikko;

CREATE TEXT SEARCH DICTIONARY voikko_stopwords (
    TEMPLATE = voikko_template, StopWords = finnish
);

CREATE TEXT SEARCH CONFIGURATION voikko (COPY = public.finnish);

ALTER TEXT SEARCH CONFIGURATION voikko 
    ALTER MAPPING FOR asciiword, asciihword, hword_asciipart, word, hword, hword_part 
    WITH voikko_stopwords, finnish_stem;
```

Test with 

```sql
select ts_lexize('voikko', 'kerrostalollekohan');
```

The result should be:

```
 ts_lexize   
-----------
 {kerros,talo}
(1 row)
```
