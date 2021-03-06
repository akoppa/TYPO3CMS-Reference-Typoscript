.. include:: ../../Includes.txt


.. _select:

======
select
======

This object generates an SQL-select statement to select records
from the database.

Some records are hidden or timed by start- and end-times. This is
automatically added to the SQL-select by looking for "enablefields"
in the :php:`$GLOBALS['TCA']`.

.. warning::

   Do not use GET or POST data like GPvar directly with this object!
   Avoid SQL injections! Don't trust any external data! Secure
   any unknown data, for example with :ref:`intval <stdwrap-intval>`.



Comprehensive example
=====================

See PHP source code for
:php:`\TYPO3\CMS\Frontend\ContentObject\ContentObjectRenderer`,
:php:`ContentObjectRenderer::getQuery()`,
:php:`ContentObjectRenderer::getWhere()`.


.. Preamble: :
   # Note: TypoScript (TS) is just another way to define an array of settings which
   #       is later on INTERPRETED by TYPO3. TypoScript can be written in ANY order
   #       as long as it leads to the same array. Actual execution order is TOTALLY
   #       INDEPENDENT of TypoScript code order.
   #
   #       The order of TS in this example however tries to reflect execution order.
   #       The denoted steps are taking place in that order at execution time.

Condensed form::

   10 = CONTENT
   10 {
      table =
      select {
         uidInList =
         pidInList =
         recursive =
         orderBy =
         groupBy =
         max =
         begin =
         where =
         languageField =
         includeRecordsWithoutDefaultTranslation =
         selectFields =
         join =
         leftjoin =
         rightjoin =
      }
   }


.. Expanded form::
   //
   See also: :ref:`if`, :ref:`select`, :ref:`data-type-wrap`, :ref:`stdWrap`, :ref:`data-type-cobject`



.. ### BEGIN~OF~TABLE ###

.. _select-uidInList:

uidInList
=========

.. container:: table-row

   Property
         uidInList

   Data type
         *list of record\_ids* / :ref:`stdWrap`

   Description
         Comma-separated list of record uids from the according database table.
         For example when the select function works on the table `tt_content`, then
         this will be uids of `tt_content` records.

         **Note:** :ts:`this` is a *special keyword* and replaced with the id of the
         *current record*.

   Example
         ::

            select.uidInList = 1,2,3
            select.uidInList = this




.. _select-pidInList:

pidInList
=========

.. container:: table-row

   Property
         pidInList

   Data type
         *list of page\_ids* / :ref:`stdWrap`

   Description
         Comma-separated list of pids of the record. This will be page uids (pids). For
         example when the select function works on the table tt_content, then this
         will be pids of tt_content records, the parent pages of these records.

         Pages in the list, which are not visible for the website user, *are
         automatically removed* from the list. Thereby no records from hidden,
         timed or access-protected pages will be selected! Nor will be records
         from recyclers. Exception: The hidden pages will be listed in *preview mode*.

         **Special keyword:** "this" is replaced with the id of the current page.

         **Special keyword:** "root" allows to select records from the root-page level
         (records with pid=0, e.g. useful for the table "sys_category" and others).

         **Special value:** "-1" allows to select versioned records in workspaces
         directly.

   Example
      Fetch related `sys_category` records stored in the MM intermediate table::

         10 = CONTENT
         10 {
            table = sys_category
            select {
               pidInList = root,-1
               selectFields = sys_category.*
               join = sys_category_record_mm ON sys_category_record_mm.uid_local = sys_category.uid
               where.data = field:_ORIG_uid // field:uid
               where.intval = 1
               where.wrap = sys_category_record_mm.uid_foreign=|
               orderBy = sys_category_record_mm.sorting_foreign
               languageField = 0 # disable translation handling of sys_category
            }
         }


   Default
         :ts:`this`


.. _select-recursive:

recursive
=========

.. container:: table-row

   Property
         recursive

   Data type
         :ref:`data-type-integer` / :ref:`stdWrap`

   Description
         Number of recursivity levels for the pidInList.

   Default
         0


.. _select-orderBy:

orderBy
=======

.. container:: table-row

   Property
         orderBy

   Data type
         *SQL-orderBy* / :ref:`stdWrap`

   Description
         ORDER BY clause without the words "ORDER BY".

   Example
         ::

            orderBy = sorting, title


.. _select-groupBy:

groupBy
=======

.. container:: table-row

   Property
         groupBy

   Data type
         *SQL-groupBy* / :ref:`stdWrap`

   Description
         GROUP BY clause without the words "GROUP BY".

   Example
         ::

            groupBy = CType



.. _select-max:

max
===

.. container:: table-row

   Property
         max

   Data type
         :ref:`data-type-integer` +calc +"total" / :ref:`stdWrap`

   Description
         Max records

         **Special keyword:** "total" is substituted with :php:`count(*)`.



.. _select-begin:

begin
=====


.. container:: table-row

   Property
         begin

   Data type
         :ref:`data-type-integer` +calc +"total" / :ref:`stdWrap`

   Description
         Begin with record number *value*.

         **Special keyword:** "total" is substituted with :php:`count(*)`.


.. _select-where:

where
=====

.. container:: table-row

   Property
         where

   Data type
         *SQL-where* / :ref:`stdWrap`

   Description
         WHERE clause without the word "WHERE".

   Example
         ::

            where = (title LIKE '%SOMETHING%' AND NOT doktype)


.. _select-languageField:

languageField
=============

.. container:: table-row

   Property
         languageField

   Data type
         :ref:`data-type-string` / :ref:`stdWrap`

   Description
         By default all records that have language-relevant information in the
         TCA "ctrl"-section are translated on translated pages.

         This can be disabled by setting :ts:`languageField = 0`.


.. _select-includeRecordsWithoutDefaultTranslation:

includeRecordsWithoutDefaultTranslation
=======================================

.. container:: table-row

   Property
         includeRecordsWithoutDefaultTranslation

   Data type
         :ref:`data-type-bool` / :ref:`stdWrap`

   Description
         If content language overlay is activated and the option :ts:`languageField` is not disabled,
         :ts:`includeRecordsWithoutDefaultTranslation` allows to additionally fetch records,
         which do **not** have a parent in the default language.

   Default
         0



.. _select-selectFields:

selectFields
============

.. container:: table-row

   Property
         selectFields

   Data type
         :ref:`data-type-string` / :ref:`stdWrap`

   Description
         List of fields to select, or :php:`count(*)`.

         If the records need to be localized, please include the
         relevant localization-fields (uid, pid, languageField and
         transOrigPointerField). Otherwise the TYPO3 internal localization
         will not succeed.

   Default
         \*



.. _select-join:
.. _select-leftjoin:
.. _select-rightjoin:

join, leftjoin, rightjoin
=========================

.. container:: table-row

   Property
         join, leftjoin, rightjoin

   Data type
         :ref:`data-type-string` / :ref:`stdWrap`

   Description
         Enter the table name for JOIN, LEFT OUTER JOIN and RIGHT OUTER JOIN
         respectively.



.. _select-markers:

markers
=======

.. container:: table-row

   Property
         markers

   Data type
         *(array of markers)*

   Description
         The markers defined in this section can be used, wrapped in the usual
         ###markername### way, in any other property of select. Each value is
         properly escaped and quoted to prevent SQL injection problems. This
         provides a way to safely use external data (e.g. database fields,
         GET/POST parameters) in a query.

         **Available sub-properties:**

         **<markername>.value:** (value)

         Sets the value directly.

         **<markername>.commaSeparatedList:** (:ref:`data-type-boolean`)

         If set, the value is interpreted as a comma-separated list of values.
         Each value in the list is individually escaped and quoted.

         **(stdWrap properties ...)**

         All stdWrap properties can be used for each markername.

   Example

         ::

            page.60 = CONTENT
            page.60 {
                table = tt_content
                select {
                    pidInList = 73
                    where = header != ###whatever###
                    orderBy = ###sortfield###
                    markers {
                        whatever.data = GP:first
                        sortfield.value = sor
                        sortfield.wrap = |ting
                    }
                }
            }

         This example selects all records from table tt_content, which are on page 73 and
         which don't have the header set to the value provided by the Get/Post variable
         "first", ordered by the content of the column "sorting".

.. ###### END~OF~TABLE ######
