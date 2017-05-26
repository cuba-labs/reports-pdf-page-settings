# How to tune PDF page settings for HTML -> PDF reports

## Basic priciples of HTML to PDF formatting in YARG / CUBA reporting:

- You can use HTML/CSS features of https://github.com/flyingsaucerproject/flyingsaucer library
- You can control page size / page headers / footers using special CSS rules and properties
- Main guide: http://flyingsaucerproject.github.io/flyingsaucer/r8/guide/users-guide-R8.html

## Useful resources:

- https://www.smashingmagazine.com/2015/01/designing-for-print-with-css/
- https://www.smashingmagazine.com/2011/11/how-to-set-up-a-print-style-sheet/

Let's imagine that we want to create a report with landscape orientation, page numbers, fixed page header and footer on each page.

We will start with HTML structure:
```
<body>
  <h1>Clients report</h1>

  <!-- Custom HTML header -->
  <div id="header">
    <h2>Annual Report of our Company</h2>
  </div>

  <!-- Custom HTML footer -->
  <div id="footer">
    <h2>Address: William Road</h2>
    <span class="custom-footer-page-number">Number: </span>
  </div>

  <#assign clients = Root.bands.Clients />

  <#list clients as client>
  <!-- New page for each client  -->
  <div class="custom-page-start" style="page-break-before: always;">
    <h2>Client</h2>

    <p>Name: ${client.fields.title}</p>
    <p>Summary: ${client.fields.summary}</p>
  </div>
  </#list>
</body>
```

Here we define header and footer blocks that will be printed on each PDF page. Also we use special `page-break-before: always` CSS property. It will generate page break before each client info block.

As you can see we use FreeMarker statements to insert data to our template. See complete FreeMarker reference here: http://freemarker.org/docs/

We will use the following CSS code to tune our PDF page representation:
```
body {
  font: 12pt Georgia, "Times New Roman", Times, serif;
  line-height: 1.3;
}

@page {
  /* switch to landscape */
  size: landscape;
  /* set page margins */
  margin: 0.5cm;
  /* Default footers */
  @bottom-left {
    content: "Department of Strategy";
  }
  @bottom-right {
    content: counter(page) " of " counter(pages);
  }
}
```

And this CSS code to set heade/footer positions:
```
/* footer, header - position: fixed */
#header {
  position: fixed;
  width: 100%;
  top: 0;
  left: 0;
  right: 0;
}

#footer {
  position: fixed;
  width: 100%;
  bottom: 0;
  left: 0;
  right: 0;
}
```

After that we need to fix paddings of main content to prevent content and header/footer overlapping:
```
/* Fix overflow of headers and content */
body {
  padding-top: 50px;
}
.custom-page-start {
  margin-top: 50px;
}
```

What if we want to insert page number to custom position? That's it:
```
.custom-footer-page-number:after {
  content: counter(page);
}
```

Full report template and sample project are hosted on GitHub: https://github.com/cuba-labs/reports-pdf-page-settings

If you want to insert images to your PDF files, see our existing documentation: https://doc.cuba-platform.com/reporting-6.5/template_html.html See `Embedded pictures` section.