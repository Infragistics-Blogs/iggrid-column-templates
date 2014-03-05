# Binding Complex Objects to an igGrid Column Template
**Demo:** [Running code sample](http://rawgithub.com/Infragistics-Blogs/iggrid-column-templates/master/index.html)

When working with the [Ignite UI](http://igniteui.com/) [igGrid](http://igniteui.com/grid/overview) you have the option to harness the column template feature in order to take control of how data is presented in a column inside the grid. While you can use the column template to make some relatively simple modifications to the data (e.g., add labels, etc.) you can also use it to take full control over the layout of a rendered column.
The following screenshot shows how to use the grid with a single column to render data with a custom layout. 

<img style="width:100%;max-width:948px" title="Demo of binding igGrid column template to complex JavaScript object." alt="" src="http://raw.github.com/Infragistics-Blogs/iggrid-column-templates/master/iggrid-column-templates.png" />

Here data for the grid is laid out using Bootstrap layout classes in order to arrange the data in the grid in more complex fashion than traditional rows and columns.

## Building the Column Template
The column templates for the igGrid are flexible enough that you can use them in a few different ways. In this example the grid is setup with a single column and the layout is fairly involved, therefore in a way you can think of this setup as being analogous to using a row template.

In the documentation many of the examples demonstrate how to use the column template with a very simple string embedded in JavaScript. While this works well to demonstrate the concepts, when building more robust applications you’ll often want to define the template in the page’s markup and then inject the HTML into the grid when the template is required.

Here is the template used to render data in the grid. It’s a bit involved so I’ll review some of the more interesting segments after you’ve had a chance to survey the entire template.

```
<script id="sessions-template" type="text/template">
    <div class="row pad-top-md pad-bottom-md pad-right-md">
        <div class="col-xs-4 col-sm-2 text-center bio-links">
            <a href="/speakers/${speakerId}/${speakerFirstName}-${speakerLastName}">
                <div><img src="${speakerImageSource}" class="img-thumbnail" /></div>
                <h4>${speakerFirstName} ${speakerLastName}</h4>
            </a>
            <a href="http://twitter.com/${speakerTwitter}" target="_blank" class="text-sm">@${speakerTwitter}</a>
        </div>                
        <div class="col-xs-8 col-sm-10">
            <div class="row">
                <a href="/sessions/${id}/${titleEncoded}">
                    <div class="col-sm-9">
                        <h3 class="session-title">${title}</h3>
                        ${description}
                    </div>
                </a>
                <div class="col-sm-2">
                    <span class="h3">${level}</span>
                    <div>${roomName}</div>
                    <div>${day}</div>
                    <div>${timeShortString}</div>
                    <div class="pad-top-md">
                        <a href="#" data-role="attend" data-id="${id}" title="Attend Session"><i class="fa fa-user"></i></a>
                    </div>
                </div>
            </div>
            <div class="row text-sm pad-top-md">
                <div class="col-sm-4 text-left"><strong>Track:</strong> <a href="/track/${trackName}"  class="btn btn-default btn-sm">${trackName}</a></div>
                <div class="col-sm-4 text-right"><strong>Tags:</strong>
                    {{each ${tagsList}}}
                    <a href='/tags/$data' class='btn btn-default btn-sm'>$data</a>
                    {{/each}}
                </div>
            </div>
        </div>
    </div>
</script>
```

You’ll notice that the template consists of a basic Bootstrap layout markup with tokens surrounded by `${}` sprinkled throughout the template. Each token corresponds to a member name in the JavaScript object that is bound to the grid.

### Looping in a Template
The binding object includes a string array named `tagsList` which includes the list of tags that are associated to the given session. The template requires looped elements to follow a few rules in order for the data to render correctly.

First, take a closer look at the loop syntax in the template:

```html
<div class="col-sm-4 text-right"><strong>Tags:</strong>
    {{each ${tagsList}}}
    <a href='/tags/$data' class='btn btn-default btn-sm'>$data</a>
    {{/each}}
</div>
```

As you review this code you’ll notice that:

1. Loop blocks are surrounded with the opening statement of `{{each ${MEMBER_NAME}}}` and closing statement of `{{/each}}` where `MEMBER_NAME` is the member of the JavaScript object that is being bound to the template, which in this case is `tagsList`.

2. You must not use double quotes inside a loop block. Double quotes will cause a syntax error as the template is compiled. Instead of using double quotes you can safely use the single quote character (`'`) in your template and they are transformed into double quotes after the template is compiled and rendered.

3. In order to gain access to the data item in each iteration you use the `$data` syntax. Also note that here you do not surround the token name with curly braces.

Now that the template is setup you can look at ways to provide the template’s markup to the grid.

##Grid Setup

The grid is created with a few settings that are required in order to render the custom template. First, take a look at the grid’s code in this example:

```javascript
$('#grid').igGrid({
    dataSource: data,
    autoGenerateColumns: false,
    localSchemaTransform: false,
    columns:[{
        width:'100%',
        template: templateMarkup
    }],
    width: '100%'
});
```
In this example the data is provided in-line via the `data` variable, but you could just as easily have had it available from any number of asynchronous requests.

The first settings to change are to turn off column auto generation and local schema transformation. As automatic column generation is disabled the columns are set up explicitly through the columns array. 

In the columns array a single column is defined and set at 100% width and the template is provided to the column. In the code you see that the column’s template is being set to `templateMarkup`. The `templateMarkup` variable is getting its value from a previous jQuery call to extract the HTML from the template on the page:

```javascript
var templateMarkup = $('#sessions-template').html();
```

[Make sure to check out the running sample.](http://rawgithub.com/Infragistics-Blogs/iggrid-column-templates/master/index.html)

## Summary
The igGrid column templates allow you a great amount of flexibility in customizing columns in simple ways like adding some basic formatting all the way to taking full control over how data is rendered in the grid.

As you work with column templates it’s often recommended to maintain your template in markup instead of strings in JavaScript and make sure to follow a few basic rules when creating loop blocks in your templates.

---

## What is Ignite UI?
![Ignite UI](http://rawgithub.com/Infragistics-Blogs/iggrid-column-templates/master/igniteui.png "Ignite UI")

[Ignite UI](http://igniteui.com/) is an advanced HTML5+ toolset that helps you create stunning, modern Web apps. Building on jQuery and jQuery UI, it primarily consists of feature rich, high-performing UI controls/widgets such as all kinds of charts, data visualization maps, (hierarchical, editable) data grids, pivot grids, enhanced editors (combo box, masked editors, HTML editor, date picker, to name a few), flexible data source connectors, and a whole lot more.  Too many to list here--check out [the site](http://igniteui.com/) for more info and to [download](https://igniteui.com/download).

Ignite UI is not just another library created in someone's free time. It is commercial-ready, extremely well-tested, tuned for top performance, designed for good UX, and backed by a thriving 20+-year-old UI software company, [Infragistics](http://www.infragistics.com/).
