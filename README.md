# BC Food Directory WordPress Plugin

[![MIT License](https://img.shields.io/github/license/bcgov/quickstart-openshift.svg)](/LICENSE)
[![Lifecycle](https://img.shields.io/badge/Lifecycle-Experimental-339999)](https://github.com/bcgov/repomountie/blob/master/doc/lifecycle-badges.md)

This repository provides an interactive directory where food Producers and Buyers can use their business BCeID to create an account. Producers will have the ability to create a profile, and add products.

Is an interactive directory, where Producers and Buyers can use their business BCeID to create an account.
Producers will have the ability to create a profile, and add products.

## Description

Creates a food directory using

* Producers
  * Products
* Buyers
  * Looking For (seeking) [Products]

## Installation

* Import files in /imports/advanced-custom-fields/ through WP Admin: Custom Fields > Tools > Import Field Groups.

### Revisions of Post types ( Producer, Products and Buyer )

A ``` new PostRevisions( ['products', 'producer', 'buyer']) ``` will initiate these custom post types to have revisions.

* This basically initiates some WordPress hooks for the cpt.
* The PostRevisions also has some static functions that allow:
  * Duplicate of post.
  * update_post ( which will create a revision );
  * update_post_status ( which allows user to update post status to draft, pending or trash )
* Revisions work in that they create a parent (public visible post) child relationship (revision)
* Once an admin publishes a child (revision), it updates the parent, and trashes the child.

## Settings

* Add moderator email if different than admin email at Menu Settings->General (BC Food Directory Settings (Moderator Email))
* Customizer settings
  * BC Food Directory Page Assignments
    * No role Dashboard page
    * Producer Dashboard page
    * Buyer Dashboard page
    * Add Product page (used in dynamic block used to display Producer products)
* BCGov Settings -> Email CHES
  * Fields
    * Auth host -> creating Bearer token
    * Client ID -> creating Bearer token
    * Client Secret -> creating Bearer token
    * CHES Endpoint (url for endpoint)
  * If the endpoint is empty, then defaults to regular wp_mail.
  * Health section
    * indicates database, queue, and SMTP service connections.
    * Tells if there is issue with CHES Services
    * Shows the Moderator email.

## CHES Email options

The Common Hosted Email Service (CHES) is used to email when in openshift
<https://bcgov.github.io/common-service-showcase/services/ches.html>

## Functional Tests

This function test list should change as more of the criteria is met for MVP.

### Roles

There are two additional roles Producer and Buyer.
These roles are enabled when the theme is first enabled, and removed when the theme is deactivated.

### Dashboards

Customizer is used to select the dashboard pages for no role (subscriber), producers and buyers.

* No user dashboard will default to wp admin or page selected.
* Producer user dashboard will default to producer cpt or page selected.
* Buyer user dashboard will default to admin dashboard or page selected.

#### Notes using dashboards with miniorange

Because miniorange uses a keycloak realm for authentication, it bypasses the mechanism for deciding the user, and redirecting to the appropriate dashboard.

In order to get around this a ajax url was created ```wp-admin/admin-ajax.php?action=mydashboard``` which when added to login redirect option in miniorange will use the same logic for redirecting users to the dashboards defined in customizer.

### Producer Profile

* Create a Producer ( Assign producer the producer role)
* Sign in as a Producer role
  * Create a Producer profile, ensure that only one profile can be published at time.
  * Create multiple products, ensure all products are assigned under user.
* Verify other producer profiles or products can't be viewed under different producer.

### Buyer Profile

* Create a Buyer ( Assign buyer the buyer role)
* Sign in as a Buyer role
  * Create a Buyer profile, ensure that only one profile can be published at time.

### Loading various public forms

* Only can view forms if logged in.
* otherwise 403?

#### Producer

* create a page with the Form (Gutenberg block) and enable Producer Form.
* Create a new producer cpt from a subscriber role.
* Once submitted then can't submit additional producer cpt.
* Once producer cpt approved by admin, subscriber -> producer role.
* Can't view form unless signed in.

#### Buyer

* create a page with the Form (Gutenberg block) and enable Buyer Form.
* Create a new Buyer cpt from a subscriber role.
* Once submitted then can't submit additional Buyer cpt.
* Once Buyer cpt approved by admin, subscriber -> Buyer role.
* Can't view form unless signed in.

#### Adding Product

* create a page with the Form (Gutenberg block) and enable Product Form.
* User has to be a producer or admin role to create new product.
* Fill in form and submit, should see product in draft mode.
* Producer should only be able to save as draft or submit for approval.
* Once product is published, producer will only be able to view.

### Product for Moderation

* Create page with Products (Gutenberg block)
* make sure tabs draft (draft), in review (pending) and live (published) show proper items.
* Check the edit links work.
* Check the submit link works from Draft, should add to in review.
* Goto Settings -> General page, update Moderation email. ensure when producer changes product from draft -> pending that email gets sent to moderator.

### Menus with access control

* When creating menus, ensure that there is a checkbox option for Non-Authenticated Menu, and Authenticated Menu
* Create menus for both auth and non auth menu.
* Ensure this menu appears in the header bar ( auth when user signed in, and non auth when logged out)
* Ensure menu in header get removed when is in mobile mode, and check menu's to ensure auth / non auth menus show up in mobile menu.

### Product Search

* Create a search page add the BC Food Directory Search gutenberg block.
* Ensure that there are some products to search, currently search only includes meta fields title, and description, unless using searchWP, then it uses the 'product' engine, and uses fields that is configured.
* Search keyword, ensure correct products appear, with relevant data.
* The search also includes filters:
  * Product category (taxonomy)
  * Additional certs (taxonomy)
  * Food safety (taxonomy)
  * Dietary Preferences (taxonomy)
  * When available for purchase (taxonomy)
  * Region (taxonomy)
  * Ownership and Operations (taxonomy from producer)
  * Delivery options (meta fields) -- This one needs a custom meta query to enable this filter.
* Search operations and order.
  * If ownership and operations tax are selected, then this is applied to the producer query, as well as a pre filter to the products search.
    * Pre filter to products basically finds all posts that meet the requirements of the ownership and operation tax.
    * It then returns all the authors from these posts, as this is the link between producer and products.
    * The products then uses a the post__in attribute to pre-filter the product search.

### Favourites

* after being logged in, on search page click on favourite to add, heart should turn blue.
* after being logged in, on search page click on favourite that has been added as favourite, to remove, heart should turn grey.

## Required additional plugins

### Miniorange

* [Miniorange developer](https://developers.miniorange.com/docs/rest-api-authentication/wordpress/developerhookscustom)
* <https://developers.miniorange.com/docs/oauth/wordpress/client>

## Gutenberg Blocks

### Icons

* This block allows to add a custom icon from [material symbols](https://github.com/marella/material-symbols/tree/main/material-symbols)

* Options are found in the inspector control, drop down to select the icon, also a font-size.
* These might be best used with the row block.  These row blocks might be best put in a group block and to either put a special style on the row or the group.

### Product Categories

* This block allows a user to add product categories widget to any part of the website
