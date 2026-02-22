# Authentication and Authorization

## Users and Authentication {#users-and-authentication}

At its core, Django uses a robust system to handle identity.

* **User Model**: By default, it provides a `User` model with fields like `username`, `password`, `email`, `first_name`, `last_name`, and `password` (which is securely hashed).

* **Session Management**:
     * **Login**: Uses `auth.login(request, user)` to create a session for the authenticated user.
     * **Logout**: Uses `auth.logout(request)` to end the user’s session.


## Permissions {#permissions}

Every time you create a model, Django automatically generates four permissions: `add`, `change`, `delete`, and `view`. You can also create custom permissions for more specific access control.

* Permissions are boolean flags that can be assigned to users or groups, determining what actions they can perform on specific models or views. 
    * For example, you might have a permission called `can_publish` that allows certain users to publish song details.
    * You can check permissions in your views using the `@permission_required` decorator or by manually checking `request.user.has_perm('songs.change_song')`.

## Groups {#groups}

Groups are a way to categorize users so you don't have to assign permissions individually.

Example: Create an "Editors" group, give that group "can_edit_song" permissions, and then simply add users to that group.

A user inherits all permissions granted to any group they belong to.

## Messages {#messages}

The messages framework allows you to store temporary "one-time" notifications for the user. These are stored in one request and displayed in the next page render (usually after a redirect).

```python
from django.contrib import messages
def create_song(request):
    # After a successful login
    messages.success(request, "Song created successfully!")
    return redirect('home')
```

## Profiles {#profiles}


The default User model is great, but real-world apps usually need more (like a bio, a profile picture, or a phone number). 

There are two main ways to handle this:

1. **Custom User Model** (Recommended)
   
    If you are starting a new project, it is best practice to swap the default User model for a custom one. This allows you to use an Email as the primary login identifier instead of a Username.

    Inherit from `AbstractUser` to keep default fields.

    Inherit from `AbstractBaseUser` if you want total control over the fields.


2. **Profile Models** (One-to-One Link)
   
    If you’ve already started a project and can't easily change the User model, you create a `Profile` model that links to the User.

    Use a `OneToOneField(User, on_delete=models.CASCADE)`.

    Use Signals (`post_save`) to automatically create a Profile instance whenever a new User is registered.

