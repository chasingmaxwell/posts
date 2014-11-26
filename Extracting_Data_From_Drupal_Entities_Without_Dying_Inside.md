#Extracting data from Drupal entities without dying inside

Have you ever had to extract data from Drupal entities? If so, I'm sure you're aware of the fact that it can be a painful process. This post presents a solution for distilling Drupal entities into human-readable documents that can be easily consumed by other modules and services.

##The dying part
Properly extracting and formatting data from Drupal entity objects can be complicated and verbose, especially if you're doing a lot of it. It's difficult to organize your extraction rules, and formatting patterns, and it's even more difficult to make these rules and patterns modular and re-useable.

Of course, you can do a pretty decent job of making your formatting rules portable, and if you're extracting and formatting only a small amount of fields, then you might be ok. But if you are dealing with a lot of entities of different types and trying to extract/format data from lots of fields of different types, then your codebase will most likely get out of control, even if you're a really good programmer. Your formatting rules will get bloated with edge cases and will be hard to mantain. You'll end up repeating yourself a lot with your actual extraction code, even if you're using the EntityMetadataWrapper.


## Enter Distill
I was recently working on a project, and this exact problem came arose. I needed to extract data from a bunch of different entity types that all referenced each other, and I needed to keep my formatting rules organized so that I could re-use them. I also needed to be able to group formatting rules in a generic way so that I could apply them to multiple entity types. So, I started working on a solution in the form of a module named [Distill](https://github.com/patrickocoffeyo/distill).

Distill contains 2 classes; a processor class that contains sensible defaults for extracting field values by field type, and a distillation class that takes an entity, a processor class, and a list of fields that should be returned, executes the processor's formatter methods, and returns an array of data. By breaking the formatter and the extractor into two different mechanisms, I was able to organize my formatting rules and make them non-specific to my data extraction. This allowed me to keep very complicated formatting rules organized and portable. The processor in which those rules are defined is subclass-able, so you can even make variations of your rules and patterns.

So how does this work?

### The Distill class

`Distill` is a class that takes an entity type, entity, processor, and language. When asked, it will go through the fields, process them with the methods as defined in the passed-in processor, and add them to an array of field values. It contains a few methods that allow you to ask for fields, and grab field values.

- `setField('field_name', 'property_name', array('settings'))`: Asks the distiller to process a given field, and add it to the array of processed fields with the key as specified in the `property_name` parameter. This function also takes a settings array, which gets passed into the processor class and allows one to give context to the formatting method.
- `setAllFields()`: Tells the distiller that all fields should be formatted and returned using the sensible defaults as defined in the passed-in processor. 
- `getFieldValues()`: Returns the array of field data that has been extracted from the given entity and processed by the distiller.

###DistillProcessor class
The `DistillProcessor` class contains methods that provide a sensible default for extracting and formatting data from fields. The methods are called based on the field type, or name.

You can create your own processor class that extends `DistillProcessor`, and easily override processor methods by simply creating methods in the following pattern:

####Field Type Processor Methods
Field type processor methods are called based on the **type** of field that's currently being processed. The pattern for creating these method names is `process*Typename*Type()`, where `*Typename*` is equal to the type of field this method should process (such as `Text` or `Entityreference`).

####Field Name Processor Methods
Field name processor methods are called based on the **name** of the field that's currently being processed. The patter for creating these method names is `process*Fieldname*Field()`, where `*Fieldname*` is equal to the name of the field this method should procces (such as `Fieldimage` or `Body`).

**All processor methods take 3 parameters:**

 - `$wrapper`: EntityStructureWrapper|EntityValueWrapper of field from which values are being extracted.
 - `$index`: Integer representing the delta of the field being processed.
 - `$settings`: Variable for passing in settings and context that will affect how the field value should be processed.
 
### Enough talk! Let's have an example!
Here's an example of Distill in use.

```
/**
 * Returns cool stuff.
 *
 * @param object $entity
 *   Entity object from which cool stuff will be extracted.
 *
 * @return string
 *   JSON object full of cool things.
 */
function my_module_return_cool_stuff($entity) {
  $entity = node_load(39);

  // Create instance of processor.
  // This can be your own (subclass) processor, with
  // your own formatting methods.
  $processor = new DistillProcessor();

  // Create instance of Distill.
  $distiller = new Distill('node', $entity, $processor);

  // Specify which fields should be returned.
  $distiller->setField('title');
  $distiller->setField('body', 'post');
  $distiller->setField('field_image', 'image');
  $distiller->setField('field_user_reference', 'user', array(
    'include_fields' => array(
      'name',
      'mail'
    )
  ));
  $distiller->setField('field_entity_reference', 'referenced_entity', array(
    'include_fields' => array(
      'title',
      'body'
    )
  ));

  // Output JSON
  return drupal_json_output($distiller->getFieldValues());
}
```

And this example code would return a blob of JSON that looks like this:

```
{
  title: "Hello World!",
  post: {
    value: "<p>This is a post body, full of really cool things.</p>"
    summary: "<p>As you can see, I'm adequately summarized.</p>"
  },
  image: "http://mysite.com/sites/default/files/field/image/post_image.jpg",
  user: {
    name: "admin",
    mail: "patrick@mysite.com"
  },
  referenced_entity: {
    title: "The World",
    body: {
      value: "<p>The world is a cool place.</p> "
    }
  }
}
```

Pretty cool, yes? And the best part about this is how 

## What's next for Distill?
I'm currently working on getting Distill up on drupal.org. For now, it's canonical version lives on github [here](https://github.com/patrickocoffeyo/distill). 

I'm also working on some code that will allow other modules to hook into Distill and provide default formatters for field types that they define.

If Distill is useful to you, check it out! And feel free to fork the repo and contribute. :)

 
 