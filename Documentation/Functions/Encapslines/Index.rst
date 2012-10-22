﻿

.. ==================================================
.. FOR YOUR INFORMATION
.. --------------------------------------------------
.. -*- coding: utf-8 -*- with BOM.

.. ==================================================
.. DEFINE SOME TEXTROLES
.. --------------------------------------------------
.. role::   underline
.. role::   typoscript(code)
.. role::   ts(typoscript)
   :class:  typoscript
.. role::   php(code)


encapsLines
^^^^^^^^^^^

.. ### BEGIN~OF~TABLE ###

.. container:: table-row

   Property
         Property:
   
   Data type
         Data type:
   
   Description
         Description:
   
   Default
         Default:


.. container:: table-row

   Property
         encapsTagList
   
   Data type
         list of strings
   
   Description
         List of tags which qualify as encapsulating tags. Must be lowercase.
         
         **Example:**
         
         ::
         
            encapsTagList = div, p
         
         This setting will recognize the red line below as encapsulated lines:
         
         ::
         
            First line of text
            Some <div>text</div>
            <p>Some text</p>
            <div>Some text</div>
            <B>Some text</B>
   
   Default


.. container:: table-row

   Property
         remapTag.[ *tagname* ]
   
   Data type
         string
   
   Description
         Enter a new tag name here if you wish the tagname of any encapsulation
         to be unified to a single tag name.
         
         For instance, setting this value to "remapTags.P=DIV" would convert:
         
         ::
         
            <p>Some text</p>
            <div>Some text</div>
         
         to
         
         ::
         
            <div>Some text</div>
            <div>Some text</div>
         
         ([ *tagname* ] is in uppercase.)
   
   Default


.. container:: table-row

   Property
         addAttributes.[ *tagname* ]
   
   Data type
         array of strings
   
   Description
         Attributes to set in the encapsulation tag.
         
         **Example:**
         
         ::
         
            addAttributes.P {
              style=padding-bottom:0px; margin-top:1px; margin-bottom:1px;
              align=center
            }
         
         ([ *tagname* ] is in uppercase.)
         
         ::
         
            .setOnly = 
         
         exists : This will set the value ONLY if the property does not already
         exist
         
         blank : This will set the value ONLY if the property does not already
         exist OR is blank ("")
         
         Default is to always override/set the attributes value.
   
   Default


.. container:: table-row

   Property
         removeWrapping
   
   Data type
         boolean
   
   Description
         If set, then all existing wrapping will be removed.
         
         This:
         
         ::
         
            First line of text
            Some <div>text</div>
            <p>Some text</p>
            <div>Some text</div>
            <B>Some text</B>
         
         becomes this:
         
         ::
         
            First line of text
            Some <div>text</div>
            Some text
            Some text
            <B>Some text</B>
   
   Default


.. container:: table-row

   Property
         wrapNonWrappedLines
   
   Data type
         wrap
   
   Description
         Wrapping for non-encapsulated lines
         
         **Example:**
         
         ::
         
            .wrapNonWrappedLines = <P>|</P>
         
         This:
         
         ::
         
            First line of text
            <p>Some text</p>
         
         becomes this:
         
         ::
         
            <P>First line of text</P>
            <p>Some text</p>
   
   Default


.. container:: table-row

   Property
         innerStdWrap\_all
   
   Data type
         ->stdWrap
   
   Description
         Wraps the content inside all lines, whether they are encapsulated or
         not.
   
   Default


.. container:: table-row

   Property
         encapsLinesStdWrap.[ *tagname* ]
   
   Data type
         ->stdWrap
   
   Description
         Wraps the content inside all encapsulated lines.
         
         ([ *tagname* ] is in uppercase.)
   
   Default


.. container:: table-row

   Property
         defaultAlign
   
   Data type
         string /stdWrap
   
   Description
         If set, this value is set as the default "align" value of the wrapping
         tags, both from .encapsTagList, .bypassEncapsTagList and
         .nonWrappedTag
   
   Default


.. container:: table-row

   Property
         nonWrappedTag
   
   Data type
         tagname
   
   Description
         For all non-wrapped lines, you can set here which tag it should be
         wrapped in. Example would be "P". This is an alternative to
         .wrapNonWrappedLines and has the advantage that it's attributes are
         set by .addAttributes as well as defaultAlign. Thus you can easier
         match the wrapping tags used for non-wrapped and wrapped lines.
   
   Default


.. ###### END~OF~TABLE ######

[tsref:->encapsLines]


((generated))
"""""""""""""

Example:
~~~~~~~~

::

   encapsLines {
     encapsTagList = div,p
     remapTag.DIV = P
     wrapNonWrappedLines = <P>|</P>
     innerStdWrap_all.ifEmpty = &nbsp;
   }

This example shows how to handle content rendered by TYPO3 and
stylesheets where the <P> tag is used to encapsulate each line.

Say, you have made this content with the Rich Text Editor:

::

   This is line # 1 
   
   [Above is an empty line!] 
   <DIV align=right>This line is right-aligned</DIV>

After being processed by encapsLines with the above configuration, the
content looks like this:

::

   <P>This is line # 1 </P>
   <P>&nbsp;</P>
   <P>[Above is an empty line!] </P>
   <P align="right">This line is right-aligned</P> 

Each line is nicely wrapped with <P> tags. The line from the database
which was  *already* wrapped (but in <DIV>-tags) has been converted to
<P>, but keeps it's alignment. Overall, notice that the Rich Text
Editor ONLY stored the line which was in fact right-aligned - every
other line from the RTE was stored without any wrapping tags, so that
the content in the database remains as human readable as possible.


Example:
~~~~~~~~

::

   # Make sure nonTypoTagStdWrap operates on content outside <typolist> and <typohead> only:
   tt_content.text.20.parseFunc.tags.typolist.breakoutTypoTagContent = 1
   tt_content.text.20.parseFunc.tags.typohead.breakoutTypoTagContent = 1
   # ... and no <BR> before typohead.
   tt_content.text.20.parseFunc.tags.typohead.stdWrap.wrap >
   # Setting up nonTypoTagStdWrap to wrap the text with P-tags
   tt_content.text.20.parseFunc.nonTypoTagStdWrap >
   tt_content.text.20.parseFunc.nonTypoTagStdWrap.encapsLines {
     encapsTagList = div,p
     remapTag.DIV = P
     wrapNonWrappedLines = <P style="margin:0 0 0;">|</P>
   
     # Forcing these attributes onto the encapsulation-tags if any
     addAttributes.P {
       style=margin:0 0 0;
     }
     innerStdWrap_all.ifEmpty = &nbsp;
     innerStdWrap_all.textStyle < tt_content.text.20.textStyle
   }
   # finally removing the old textstyle formatting on the whole bodytext part.
   tt_content.text.20.textStyle >
   # ... and <BR>-tag after the content is not needed either...
   tt_content.text.20.wrap >

This is an example of how to wrap traditional tt\_content bodytext
with <P> tags, setting the line-distances to regular space like that
generated by a <BR> tag, but staying compatible with the RTE features
such as assigning classes and alignment to paragraphs.
