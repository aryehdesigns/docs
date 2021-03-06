---
title: May the Forms Be With You
id: 067c3e30-423b-41bf-a224-09ebed2c3158
cover: /assets/img/trail-guides/forms.jpg
state: complete
description: Create, validate, collect, analyze, and export data with Statamic forms.
overview: |
  Forms are a natural part of the internet experience and a core component of most websites in some shape or form. From "Contact Us" to "Vote for Your Favorite Naked Mole Rat", Statamic can help manage your forms and make your life a little easier.
---
## Overview

Statamic forms serve to collect, report, and reuse user submitted data. The end-to-end solution includes Tags, settings, and a dedicated area of the Control Panel. These features together effectively replace v1's first-party form module, Raven. And yes, "Forms" is a pretty boring title. But at least it's fairly clear as to what it does.

## Your first form {#create}

Let's pretend, shall we, that you are a famous celebrity with a large following of dedicated fans. If that is true of you -- kudos by the way -- but why are you building your own website? You don't have time for that. Who's going to sail your yacht?

Okay let's just pretend you're a famous celebrity's _web developer_. You've been tasked with collecting electronic fan mail (we'll call it ef-Mail). You want to collect the following bits of info from <del>crazed</del> enthusiastic fans:

- name
- age
- level of adoration (appreciation, fixation, or obsession)
- message

### Create the formset

First, head to `/cp/forms` in the Tools area of the Control Panel and click the **Create Form** button. Alternately you can create a `.yaml` file in `site/settings/formsets` which will contain all the form fields and settings.

Each form should contain a title and a set of fields with [validation rules][rules]. Optionally it may also have metrics
and email configuration.

Once configured you will have a formset file that looks something like this:

```.language-yaml
title: Super Fans

fields:
  name:
    display: Name
    validate: required
  age:
    display: Favorite Number
    validate: required|integer
  adoration:
    display: Level of Adoration
    validate: required
  message:
    display: Message
    validate: required
```

Next you can check out some of the other [Form Tags][tags] available to you to display any errors, show a success message, and so on. We'll keep this example brief and you can explore those at leisure.

### The Template

Next, we need to set up the form in your template. There are two basic ways to markup your form's HTML. The first is to loop through your form's fields like so:

```
{{ form:create in="superfans" }}
  {{ fields }}
    <label>{{ display }}</label>
    <input type="text" name="{{ field }}" value="{{ old }}" />
  {{ /fields }}
{{ /form:create }}
```

Alternately, you can take a full-control approach and write out all the inputs yourself explicitly. This is useful when all of your fields aren't uniform -- when certain aspects require more control. In this case, your template might look more like this:

```
{{ form:create in="superfans" }}
  <label>Name</label>
  <input type="text" name="name" value="{{ old:name }}" />

  <label>Age</label>
  <input type="text" name="age" value="{{ old:age }}" />

  <label>Level of Adoration</label>
  <select name="adoration">
    <value>Appreciation</value>
    <value>Fixation</value>
    <value>Obsession</value>
  </select>

  <label>Your Message</label>
  <textarea name="message">{{ old:message }}</textarea>

{{ /form:create }}
```


## Viewing submissions {#viewing-submissions}

The Control Panel enables you to explore the collected responses, configure dashboards with reporting metrics, and export that data to CSV or JSON formats.

![Forms](/assets/img/screenshots/cp-forms.png)

You can jump back into your form's settings and pick which fields you want shown and set up metrics you'd like shown above your responses. Here's an example:

![Metrics](/assets/img/screenshots/cp-metrics.png)

These metrics would have a configuration like so:

```language-yaml
metrics:
  -
    type: total
    label: Total Responses
  -
    type: average
    field: age
    label: Average Age
    precision: 1
```

You have access to `average`, `sum`, `min`, `max`, and `total` metrics that can be applied to any field in your form.

## Displaying submission data

You can display any or all of the submissions of your forms on the front-end of your site using the [formsubmissions][submissions] Tag.

```
<h2>My fans have said some things you can't forget...<h2>
{{ form:submissions in="superfans" limit="15" }}
  {{ message | markdown }}
{{ /form:submissions }}
```

## Exporting your data

Exporting your data is as easy as click the **Export** button when viewing your form in the Control Panel. You have the choice between CSV and JSON. Choose wisely.

## Emails

Allowing your fans to send their comments is all well and good, but at this point you will only know about it when you
head back into the Control Panel to view the submissions. Wouldn't it be better to get notified? It's simple to send
an email when a form is submitted.

You can add any number of emails to your formset.

``` .language-yaml
email:
  -
    to: hello@celebrity.com
    from: website@celebrity.com
    subject: You've got fan mail!
    template: fan-mail
  -
    to: agent@celebrity.com
    subject: Someone still likes your client
    automagic: true
```

Here we'll send two emails for every submission of this form. One will go to the celebrity, and one to the agent.
The first one uses a Statamic template, the other gets an "automagic" email. The automagic email will be a simple
list of all the fields and values in the submission.

[Learn how to create your emails](/docs/recipes/emails)


## Roadmap

- Various Event Triggers
- File upload support
- Additional metrics (e.g. Tally)

_Note: Editing content (Pages, Entries, etc) is not in the scope of the Forms addon. It is different than Raven in that regard. That is a separate feature entirely._

[rules]: https://laravel.com/docs/5.1/validation#available-validation-rules
[tags]: /docs/tags/form
[submissions]: /docs/tags/form-submissions
