Data Flow
---------

* Unique link is validated. If the URL is not valid anymore User is redirected to marketing home page.
* If the current_user is a member of the Siminar/Bundle, User is redirected to the item's current step.
* If the user is anonymous, the cookie is checked for any existing user Ident.
* If ident is not present, a new Cookie == Ident is generated and stored in User's browser.
* Check if the ident matches 
