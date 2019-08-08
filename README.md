# UmbProfile CSRF PoC

This Visual Studio solution is a proof of concept for a CSRF vulnerability that exists in Umbraco versions 6.2.0-7.12.4. The vulnerability enables an attacker to update a logged in member's profile by tricking them into submitting a form on a site that the attacker controls.

There are two projects in the solution:

* A target Umbraco 7.12.4 site that contains the standard forms for registering, logging in, and updating your profile.
  * Backoffice username: admin@example.com
  * Backoffice password: Admin1234!
* A basic MVC site with a CTA on the home page that submits a hidden form.

To see the exploit in action perform the following steps:

1. Fire up the target site. A SQL CE database has been included so it should *just run*.
2. Register an account. You will be automatically logged in and brought to the edit profile page.
3. Start the attacker site in a new tab.
4. Click the large button on the home page. This will result in a YSOD.
5. Head back to the edit profile page on the target site and refresh. Your profile should have been updated.

This exploit takes advantage of the publicly exposed `HandleUpdateProfile` method on the `UmbProfileController` that ships with Umbraco. By constructing a form that POSTs to `/Umbraco/Surface/UmbProfile/HandleUpdateProfile` on the target site, we can modify the editable properties of the currently logged in member, which includes name and email.

We can take the PoC a step further by adding some JavaScript to automatically submit the form, so the victim only needs to be tricked into visiting the page in the first place. See the *Index.cshtml* view for an example.
