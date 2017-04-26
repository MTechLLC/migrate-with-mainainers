<!-- .slide: class="center" data-state="baltimore-city"-->
# Migrate with the Maintainers
## Lab

 - Adam Hoenich (phenaproxima)
 - Lucas Hedding (heddn)
 - Mike Ryan (mikeryan)
 - Ryan Weal (Ryan Weal)

Formatted as slides here:
https://mtechllc.github.io/migrate-with-mainainers/#/



<!-- .slide: class="align-left heading-space body-color-dark" data-state="baltimore-bird" -->
## What is migrate?

 - A method to import content and configuration
     - Configuration from an old D6 site
     - Term data from CSV
     - Hierarchy of data from XML
     - Etc.



<!-- .slide: class="align-left heading-space body-color-dark" data-state="baltimore-bird" -->
## Historical Note

 - Forget what you know about migrate for Drupal 7
 - Migrate in core is a pluggable Extract, Transform, Load (ETL) process



<!-- .slide: class="align-left heading-space body-color-dark" data-state="baltimore-bird" -->
## What is migrate?

 - A method to extract content and configuration, transform it and load into Drupal 8
     - Soure plugin (extract)
     - Process pluggin (transform)
     - Destination plugin (load)



<!-- .slide: class="align-left heading-space body-color-dark" data-state="baltimore-bird" -->
## What is migrate?
### Source Plugin
 - Extract data from somewhere
 - (optionally) accepts some configuration to locate the data and/or format it
 - Examples:
    - SQL
    - CSV
    - XML
    - JSON
 
Note:
SQL is in core, the rest are in contrib.



<!-- .slide: class="align-left heading-space body-color-dark" data-state="baltimore-bird" -->
## Source process plugin
### Core examples
 - block
 - d7_field_instance
 - d6_node_settings
 - d7_url_alias
 - +140  more for all your Drupal to Drupal needs

Note:
Almost all of core's source plugins extend from SQL.



<!-- .slide: class="align-left heading-space body-color-dark" data-state="baltimore-bird" -->
## What is migrate?
### Process Plugin
 - Transform data
 - (optionally) accepts some configuration
 - Examples:
    - get
    - explode
    - concat
    - callback
    - entity_lookup or entity_generate
 
Note:
 - The full list of re-usable plugins in core (and several in contrib) are at: https://www.drupal.org/docs/8/api/migrate-api/migrate-process/migrate-process-overview
- The last one, plus several others are in Migrate Plus



<!-- .slide: class="align-left heading-space body-color-dark" data-state="baltimore-bird" -->
## What is migrate?
### Destination Plugin
 - Load data into the destination
 - (optionally) accepts some configuration
 - Examples:
   - EntityContentBase
   - EntityConfigBase
   - EntityReferenceRevisions
   
Note:
 - Node, terms, users, etc are content.
- Field instances, field base, strongarm variables, etc are configuration.
- Almost all of these exist in core. The lone none exception is for Paragraphs, which stores its data in entity_reference_revisions.
- See https://www.mtech-llc.com/blog/charlotte-leon/migration-csv-data-paragraphs for a full write-up on how to migrate into Paragraphs.



<!-- .slide: class="align-left heading-space body-color-dark" data-state="baltimore-bird" -->
## Modules
### Core
  - migrate
  - migrate_drupal
  - migrate_drupal_ui

Note:
  - Only migrate is needed if you aren't importing data from a previous Drupal site.
- Only migrate_drupal is needed if you plan to import using drush.



<!-- .slide: class="align-left heading-space body-color-dark" data-state="baltimore-bird" -->
## Modules
### Contrib
  - migrate_plus
    - Store migration as config entities
    - Import from JSON / XML
  - migrate_tools
    - Run migrations from command line (drush)
  - migrate_source_csv
    - Import CSV data
  - wordpress_migrate

Note:
 - Other source plugins exist in contrib, but these are the most popular.
- Of other note for running migrations: migrate_manifest



<!-- .slide: class="align-left heading-space body-color-dark" data-state="baltimore-bird" -->
## Migration approaches

<table border="1" cellpadding="1" cellspacing="1" style="width: 100%;">
  <tbody>
    <tr>
      <td>Automatic</td>
      <td>Hybrid</td>
      <td>Manual</td>
    </tr>
    <tr>
      <td>
      <ul>
         <li>migrate</li>
         <li>migrate_drupal</li>
         <li>migrate_drupal_ui</li>
       </ul>
      </td>
      <td>
      <ul>
        <li>migrate</li>
        <li>migrate_drupal</li>
        <li>migrate_upgrade</li>
        <li>migrate_plus (contrib)</li>
        <li>migrate_tools (contrib)</li>
      </ul>
      </td>
      <td>
        <ul>
          <li>migrate</li>
          <li>migrate_drupal</li>
          <li>migrate_upgrade</li>
          <li>migrate_plus (contrib)</li>
          <li>migrate_tools (contrib)</li>
          <li>migrate_source_csv (contrib)</li>
        </ul>
      </td>
    </tr>
  </tbody>
</table>

Note:
 - Automatic: Let Drupal detect whatever it can migrate, run all the possible migrations.
- Hybrid: Let Drupal detect what it can migrate, and then modify that configuration before running it.
- Manual: Construct your own migrations, defined in configuration-like files. If for D2D migrations, get a life and use Hybrid approach.



<!-- .slide: class="align-left heading-space body-color-dark" data-state="baltimore-bird" -->
## Automatic Method

<blockquote>As a goat, I don't want to do any configuration or coding.  I just
want my content from Drupal 6 (or 7) and I want it now. Why would I want to install Drush?</blockquote>

At the end of this section you will know how to run migrations using the UI.



<!-- .slide: class="align-left heading-space body-color-dark" data-state="baltimore-bird" -->
## Automatic Method Setup

 - Modules: migrate, migrate_drupal, migrate_drupal_ui
 - Visit /upgrade after enabling these modules
 - Input DB and file paths
 - Run it.

<img class="stretch" src='images/figure4.png'>



<!-- .slide: class="heading-space body-color-dark" data-state="baltimore-bird" -->
## Automatic Method
Import success!

Add D7 hash to settings.php!

<img class="stretch" src='images/figure8.png'>



<!-- .slide: class="align-left heading-space body-color-dark" data-state="baltimore-bird" -->
## Automatic Method Extras

Possible to pre-fill database values in UI using hook_form_alter:

    /**
     * Implements hook_form_alter().
     */
    function migrate_presets_form_alter(&$form, \Drupal\Core\Form\FormStateInterface $form_state, $form_id) {
      if ($form_id == 'migrate_drupal_ui_form') {
        $form['database']['settings']['mysql']['host']['#default_value'] = 'db';
        $form['database']['settings']['mysql']['database']['#default_value'] = 'cooks_legacy';
        $form['database']['settings']['mysql']['username']['#default_value'] = 'root';
        $form['database']['settings']['mysql']['password']['#value'] = 'my-secret-pw';
        $form['database']['settings']['mysql']['advanced_options']['port']['#default_value'] = '3306';
        $form['source']['source_base_path']['#default_value'] = '/path/to/files';
      }

There is also a settings.php hack but it does not work for UI.



<!-- .slide: class="align-left heading-space body-color-dark" data-state="baltimore-bird" -->
## Automatic Method: Further reading

 - [Performing Drupal Content Migrations on Pantheon](https://pantheon.io/blog/performing-drupal-content-migrations-pantheon)



<!-- .slide: class="align-left" data-state="baltimore-docks" -->
## Hybrid Method

<blockquote> As a goat, I know writing code from scratch can take a long time,
but migrate can detect all of my configuration so I can skip that. I only want
to run part of it. Or I want to change it just a bit.</blockquote>

At the end of this section you will have working knowledge of how to use drush
for migrations.



<!-- .slide: class="align-left" data-state="baltimore-docks" -->
## Hybrid Method Setup
### Migrating from Drupal 6 or 7
- Enable: migrate, migrate_drupal, migrate_tools, migrate_upgrade, migrate_plus
- Add legacy database to settings.php.
- Install drush

<pre><code>drush migrate-upgrade --legacy-db-key=d6 --legacy-root=/files/path</code></pre>

     $databases['d6']['default'] = [
      'database' => 'cooks_legacy',
      'username' => 'root',
      'password' => 'my-secret-pw',
      'prefix' => '', 
      'host' => 'db',
      'port' => '3306',
      'namespace' => 'Drupal\Core\Database\Driver\mysql',
      'driver' => 'mysql',
    ];



<!-- .slide: class="align-left" data-state="baltimore-docks" -->
## Hybrid Method Discovery

 - Create the config, only run a few? or,
 - Export the config
 - Modify the config
 - Import the new config
 - Run the config



<!-- .slide: class="align-left" data-state="baltimore-docks" -->
## Hybrid Workflow: Export

Configure the Migrations

    $ drush migrate-upgrade --configure-only --legacy-db-key=d6 --legacy-root=/files/path

Then reap profits:

     $ drush migrate-status                                                    
     Group: migrate_drupal_6                         Status  Total  Imported  Unprocessed  Last imported 
     upgrade_block_content_type                      Idle    1      0         1                          
     upgrade_contact_category                        Idle    0      0         0                          
     upgrade_d6_date_formats                         Idle    0      0         0                          
     upgrade_d6_dblog_settings                       Idle    0      0         0                          
     upgrade_d6_file_settings                        Idle    0      0         0                          
     upgrade_d6_imagecache_presets                   Idle    3      0         3                          
     upgrade_d6_search_settings                      Idle    0      0         0                          
     upgrade_d6_system_cron                          Idle    0      0         0                          
     upgrade_d6_system_date                          Idle    1      0         1                          
     upgrade_d6_system_file                          Idle    1      0         1                          
     upgrade_d6_system_image                         Idle    1      0         1                          
     upgrade_d6_system_image_gd                      Idle    0      0         0                          
     upgrade_d6_system_logging                       Idle    0      0         0                          
     upgrade_d6_system_maintenance                   Idle    0      0         0                          
     upgrade_d6_system_performance                   Idle    0      0         0                          
     upgrade_d6_system_rss                           Idle    0      0         0                          
     upgrade_d6_system_site                          Idle    1      0         1                          
     upgrade_d6_url_alias                            Idle    0      0         0                          
     upgrade_d6_user_mail                            Idle    0      0         0                          
     upgrade_d6_user_settings                        Idle    1      0         1                          
     upgrade_menu_settings                           Idle    0      0         0                          
     upgrade_search_page                             Idle    0      0         0                          
     upgrade_taxonomy_settings                       Idle    0      0         0                          
     upgrade_text_settings                           Idle    0      0         0                          
     upgrade_update_settings                         Idle    0      0         0                          
     upgrade_block_content_body_field                Idle    1      0         1                          
     upgrade_d6_contact_settings                     Idle    0      0         0                          
     upgrade_menu                                    Idle    4      0         4                          
     upgrade_d6_filter_format                        Idle    2      0         2                          
     upgrade_d6_custom_block                         Idle    0      0         0                          
     upgrade_d6_user_role                            Idle    2      0         2                          
     upgrade_d6_block                                Idle    36     0         36                         
     upgrade_d6_file                                 Idle    683    0         683                        
     upgrade_d6_user_picture_file                    Idle    0      0         0                          
     upgrade_user_picture_field                      Idle    1      0         1                          
     upgrade_user_picture_field_instance             Idle    1      0         1                          
     upgrade_user_picture_entity_display             Idle    1      0         1                          
     upgrade_user_picture_entity_form_display        Idle    1      0         1                          
     upgrade_d6_user                                 Idle    4      0         4                          
     upgrade_d6_node_type                            Idle    6      0         6                          
     upgrade_d6_node_settings                        Idle    1      0         1                          
     upgrade_d6_field                                Idle    12     0         12                         
     upgrade_d6_field_instance                       Idle    15     0         15                         
     upgrade_d6_field_instance_widget_settings       Idle    15     0         15                         
     upgrade_d6_view_modes                           Idle    3      0         3                          
     upgrade_d6_field_formatter_settings             Idle    33     0         33                         
     upgrade_d6_upload_field                         Idle    1      0         1                          
     upgrade_d6_upload_field_instance                Idle    1      0         1                          
     upgrade_d6_node_blog                            Idle    0      0         0                          
     upgrade_d6_node_chef_profile                    Idle    3      0         3                          
     upgrade_d6_node_page                            Idle    0      0         0                          
     upgrade_d6_node_poll                            Idle    0      0         0                          
     upgrade_d6_node_recipe                          Idle    4      0         4                          
     upgrade_d6_node_story                           Idle    2      0         2                          
     upgrade_d6_comment_type                         Idle    2      0         2                          
     upgrade_d6_comment_field                        Idle    2      0         2                          
     upgrade_d6_comment_field_instance               Idle    3      0         3                          
     upgrade_d6_comment_entity_display               Idle    3      0         3                          
     upgrade_d6_comment_entity_form_display          Idle    3      0         3                          
     upgrade_d6_comment                              Idle    0      0         0                          
     upgrade_d6_comment_entity_form_display_subject  Idle    2      0         2                          
     upgrade_d6_node_revision_blog                   Idle    0      0         0                          
     upgrade_d6_node_revision_chef_profile           Idle    0      0         0                          
     upgrade_d6_node_revision_page                   Idle    0      0         0                          
     upgrade_d6_node_revision_poll                   Idle    0      0         0                          
     upgrade_d6_node_revision_recipe                 Idle    0      0         0                          
     upgrade_d6_node_revision_story                  Idle    0      0         0                          
     upgrade_d6_node_setting_promote                 Idle    6      0         6                          
     upgrade_d6_node_setting_status                  Idle    6      0         6                          
     upgrade_d6_node_setting_sticky                  Idle    6      0         6                          
     upgrade_user_profile_field                      Idle    0      0         0                          
     upgrade_user_profile_field_instance             Idle    0      0         0                          
     upgrade_user_profile_entity_display             Idle    0      0         0                          
     upgrade_user_profile_entity_form_display        Idle    0      0         0                          
     upgrade_d6_profile_values                       Idle    0      0         0                          
     upgrade_d6_taxonomy_vocabulary                  Idle    0      0         0                          
     upgrade_d6_taxonomy_term                        Idle    0      0         0                          
     upgrade_d6_upload                               Idle    0      0         0                          
     upgrade_d6_upload_entity_display                Idle    1      0         1                          
     upgrade_d6_upload_entity_form_display           Idle    1      0         1                          
     upgrade_d6_user_contact_settings                Idle    4      0         4                          
     upgrade_d6_vocabulary_field                     Idle    0      0         0                          
     upgrade_d6_vocabulary_field_instance            Idle    0      0         0                          
     upgrade_d6_vocabulary_entity_display            Idle    0      0         0                          
     upgrade_d6_vocabulary_entity_form_display       Idle    0      0         0                          
     upgrade_menu_links                              Idle    0      0         0         



<!-- .slide: class="align-left" data-state="baltimore-docks" -->
## Hybrid Workflow: Export

Export as config

    drush config-export

Everything should now be in your config folder.



<!-- .slide: class="align-left" data-state="baltimore-docks" -->
## Hybrid Workflow: Remix
Edit migrate_plus.migration.d6_user.yml

    id: d6_user
    label: User accounts
    migration_tags:
      - Drupal 6
    source:
      plugin: d6_user
    process:
      uid: uid 
      name: name
      pass: pass
      mail: mail
      created: created
      access: access
      login: login
      status: status
      timezone:
        plugin: user_update_7002
        source: timezone
      preferred_langcode: language
      init: init
      roles:
        plugin: migration
        migration: d6_user_role
        source: roles
      user_picture:
        plugin: migration
        migration: d6_user_picture_file
        source: uid 
        no_stub: true
    destination:
      plugin: entity:user
      md5_passwords: true
    migration_dependencies:
      required:
        - d6_user_role
      optional:
        - d6_user_picture_file
        - user_picture_entity_display
        - user_picture_entity_form_display



<!-- .slide: class="align-left" data-state="baltimore-docks" -->
## Hybrid Workflow: Process Chain

Implicit get plugin:

    process:
      title: subject
   
Specific process plugin: 

    process:
      uid:
        plugin: migration
        migration: users
        source: author

Chained process: 

      id:
        -
          plugin: concat
          source:
            - theme
            - module
            - delta
          delimiter: _
        -
          plugin: machine_name
          field: id



<!-- .slide: class="align-left" data-state="baltimore-docks" -->
## Hybrid Workflow: run the migrations

Import your configuration if you made changes.

    drush config-import
    
Flush cache

    drush cr

Run all migrations!

    drush migrate-import --all

Or run only a single migration:

    drush migrate-import d6_user



<!-- .slide: class="align-left" data-state="baltimore-docks" -->
## Hybrid Workflow: Further Reading

 - [Custom Drupal-to-Drupal Migrations with Migrate Tools](https://drupalize.me/blog/201605/custom-drupal-drupal-migrations-migrate-tools)
 - [Categorizing Migrations According to Their Type](https://www.mtech-llc.com/blog/edys-meza/categorizing-migrations-according-their-type)
 - [Config Management & Migrations](https://www.mtech-llc.com/blog/lucas-hedding/config-management-migrations)



<!-- .slide: class="align-left" data-state="baltimore-promenade" -->
## Manual Method

<blockquote> As a goat, I have narrowed my use case to just what I need and I would like to be all zen and do it from scratch. If I'm doing a Drupal 2 Drupal migration, I customarily am told to "get a life".</blockquote>

### All the details of configuring a migration.



<!-- .slide: class="align-left" data-state="baltimore-promenade" -->
## Manual Method Setup
### Migrating from everything else

 - Enable: migrate, migrate_drupal, migrate_tools, migrate_plus, migrate_source_csv
 - Install drush
 - For DB migrations, add legacy database to settings.php:
 

     $databases['d6']['default'] = [
      'database' => 'cooks_legacy',
      'username' => 'root',
      'password' => 'my-secret-pw',
      'prefix' => '', 
      'host' => 'db',
      'port' => '3306',
      'namespace' => 'Drupal\Core\Database\Driver\mysql',
      'driver' => 'mysql',
    ];

Note:
 - You can import from multiple DBs by using migration groups and corresponding
configuration.



<!-- .slide: class="align-left" data-state="baltimore-promenade" -->
## Manual Method Custom
### Module

We are not going to use the database we put into settings.php. We will
just use a text file called animals.csv.

    $ tree
    .
    ├── animals.csv
    ├── config
    │   └── install
    │       └── migrate_plus.migration.animals.yml
    └── migrate_demo.info.yml



<!-- .slide: class="align-left" data-state="baltimore-promenade" -->
## Manual Method Custom
### CSV

The CSV file only has one column.

    $ cat animals.csv 
    lion
    tiger
    elephant
    monkey




<!-- .slide: class="align-left" data-state="baltimore-promenade" -->
## Manual method, after enabling

    # drush ms                                             
    Group: default  Status  Total  Imported  Unprocessed  Last imported 
    animals         Idle    4      0         4   

Now import!

    # drush mi animals
    Processed 4 items (4 created, 0 updated, 0 failed, 0 ignored) - done    [status]
    with 'animals'

<img src='images/csv_result.png'>



<!-- .slide: class="align-left" data-state="baltimore-promenade" -->
## Migration Represented as Config
### Migrate Plus

    $ cat migrate_plus.migration.animals.yml 
    # The source data is in CSV files, so we use the 'csv' source plugin.
    id: animals
    label: CSV file migration
    migration_tags:
      - CSV
    source:
      plugin: csv
      # Full path to the file.
      path: /var/www/html/modules/migrate_demo/animals.csv
      header_row_count: 0
      keys:
        - id
      column_names:
        0:
          id: Identifier
    destination:
      plugin: entity:node
    process:
      type:
        plugin: default_value
        default_value: animal
      title: id 



<!-- .slide: class="align-left" data-state="baltimore-promenade" -->
## Manual Method: Further Reading

 - [Drupal 6 to Drupal 8(.1.x) Custom Content Migration](https://www.drupaleasy.com/blogs/ultimike/2016/04/drupal-6-drupal-81x-custom-content-migration)
 - [Migrating Date Ranges into Date Range Module](https://www.mtech-llc.com/blog/gerardo-hernandez/migrating-date-ranges-csv-date-range-module)
 - [Migration of Data into Paragraphs](https://www.mtech-llc.com/blog/charlotte-leon/migration-csv-data-paragraphs)
 - [How to Migrate Images](https://www.mtech-llc.com/blog/ada-hernandez/how-migrate-images-drupal-8-using-csv-source)



<!-- .slide: class="align-left" data-state="baltimore-promenade" -->
## Migrations are Plugins

<blockquote> As a goat, I want to know every detail of what I am working with, extend it in new ways, and contribute back to the community.</blockquote>

### A brief introduction to plugins.



<!-- .slide: class="align-left" data-state="baltimore-promenade" -->
## This whole talk was about Plugins

### Who knew?

    $ tree
    .
    ├── migrate_drupal.info.yml
    ├── migrate_drupal.module
    ├── migrate_drupal.services.yml
    ├── src
    │   ├── Annotation
    │   │   └── MigrateCckField.php
    │   ├── MigrationConfigurationTrait.php
    │   ├── MigrationCreationTrait.php
    │   ├── Plugin
    │   │   ├── migrate
    │   │   │   ├── cckfield
    │   │   │   │   └── CckFieldPluginBase.php
    │   │   │   ├── CckMigration.php
    │   │   │   ├── destination
    │   │   │   │   └── EntityFieldStorageConfig.php
    │   │   │   └── source
    │   │   │       ├── d6
    │   │   │       │   └── i18nVariable.php
    │   │   │       ├── d7
    │   │   │       │   └── FieldableEntity.php
    │   │   │       ├── d8
    │   │   │       │   └── Config.php
    │   │   │       ├── DrupalSqlBase.php
    │   │   │       ├── EmptySource.php
    │   │   │       ├── VariableMultiRow.php
    │   │   │       └── Variable.php
    │   │   ├── MigrateCckFieldInterface.php
    │   │   ├── MigrateCckFieldPluginManagerInterface.php
    │   │   └── MigrateCckFieldPluginManager.php
    │   └── Tests
    │       └── StubTestTrait.php
    └── tests
        ├── fixtures
        │   ├── drupal6.php
        │   └── drupal7.php
        ├── modules
        │   ├── migrate_cckfield_plugin_manager_test
        │   │   ├── migrate_cckfield_plugin_manager_test.info.yml
        │   │   └── src
        │   │       └── Plugin
        │   │           └── migrate
        │   │               └── cckfield
        │   │                   ├── D6FileField.php
        │   │                   └── D6NoCoreVersionSpecified.php
        │   └── migrate_overwrite_test
        │       ├── migrate_overwrite_test.info.yml
        │       └── migration_templates
        │           └── users.yml
        └── src
            ├── Kernel
            │   ├── d6
            │   │   ├── EntityContentBaseTest.php
            │   │   └── MigrateDrupal6TestBase.php
            │   ├── d7
            │   │   └── MigrateDrupal7TestBase.php
            │   ├── dependencies
            │   │   └── MigrateDependenciesTest.php
            │   ├── MigrateCckFieldPluginManagerTest.php
            │   ├── MigrateDrupalTestBase.php
            │   └── Plugin
            │       └── migrate
            │           └── source
            │               └── d8
            │                   └── ConfigTest.php
            └── Unit
                └── source
                    ├── d6
                    │   ├── Drupal6SqlBaseTest.php
                    │   └── i18nVariableTest.php
                    ├── DrupalSqlBaseTest.php
                    ├── VariableMultiRowSourceWithHighwaterTest.php
                    ├── VariableMultiRowTestBase.php
                    ├── VariableMultiRowTest.php
                    └── VariableTest.php



<!-- .slide: class="align-left" data-state="baltimore-promenade" -->
## Plugin Structure
### Single file, no registration, just rebuild cache.

    $ cat Concat.php 
    <?php
    
    namespace Drupal\migrate\Plugin\migrate\process;
    
    use Drupal\migrate\MigrateException;
    use Drupal\migrate\MigrateExecutableInterface;
    use Drupal\migrate\ProcessPluginBase;
    use Drupal\migrate\Row;
    
    /**
     * Concatenates a set of strings.
     *
     * @MigrateProcessPlugin(
     *   id = "concat",
     *   handle_multiples = TRUE
     * )
     */
    class Concat extends ProcessPluginBase {
    
      /**
       * {@inheritdoc}
       */
      public function transform($value, MigrateExecutableInterface $migrate_executable, Row $row, $destination_property) {
        if (is_array($value)) {
          $delimiter = isset($this->configuration['delimiter']) ? $this->configuration['delimiter'] : '';
          return implode($delimiter, $value);
        }
        else {
          throw new MigrateException(sprintf('%s is not an array', var_export($value, TRUE)));
        }
      }
    
    }

Note:
 Key is to update the annotation (comment) as it is processed.



<!-- .slide: class="align-left" data-state="baltimore-promenade" -->
## Plugins: Further Reading

 - [Migrate process overview](https://www.drupal.org/docs/8/api/migrate-api/migrate-process/migrate-process-overview)




<!-- .slide: class="heading-space body-color-dark" data-state="baltimore-bird" -->
- Adam Hoenich (phenaproxima) [@djphenaproxima](https://twitter.com/djphenaproxima)
 <a href="https://acquia.com"><img style="vertical-align: middle;" height=80px src=images/acquia.svg></a>
- Lucas Hedding (heddn) [@lucashedding](https://twitter.com/lucashedding)
 <a href="https://www.mtech-llc.com"><img style="vertical-align: middle;" height=80px src=images/mtech.svg></a>
- Mike Ryan (mikeryan) [@VirtPerformance](https://twitter.com/VirtPerformance)
 <a href="http://virtuoso-performance.com">Virtuoso Performance</a>
- Ryan Weal (Ryan Weal)
 <a href="https://kafei.ca"><img style="vertical-align: middle;" height=80px src=images/kafei.png></a>

Slidedeck liberaly inspired by [@ryan_weal](https://twitter.com/ryan_weal)'s [Migrate Shotgun Tour](https://github.com/kafeiinteractif/shotgun-migrate-tour) presentation.
