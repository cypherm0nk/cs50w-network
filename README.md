

# Project 4 - Network
 
## Web Programming with Python and JavaScript

### Harvard University | EDX

- [About Project](#about-project)
- [Requirements](#requirements)
- [Project Files](#project-files)
	- [layout](#layout)
	- [index](#index)
	- [post_form](#post_form)
	- [following](#following)
	- [post_form](#post_form)
	- [profile](#profile)
	- [register](#register)
	- [library](#library)
	- [style](#style)
	- [views](#views)
	- [urls](#urls)


## About Project
This is the documentation for my project 4 where I created a social network.
## Requirements
Below I will explain each of the requirements and the files that meet them.
#### Requirement 1
**New Post**: Users who are signed in should be able to write a new text-based post by filling in text into a text area and then clicking a button to submit the post.
-   The screenshot at the top of this specification shows the “New Post” box at the top of the “All Posts” page. You may choose to do this as well, or you may make the “New Post” feature a separate page.
#### Requirement 2
**All Posts**: The “All Posts” link in the navigation bar should take the user to a page where they can see all posts from all users, with the most recent posts first.

-   Each post should include the username of the poster, the post content itself, the date and time at which the post was made, and the number of “likes” the post has (this will be 0 for all posts until you implement the ability to “like” a post later).
#### Requirement 3
**Profile Page**: Clicking on a username should load that user’s profile page. This page should:

-   Display the number of followers the user has, as well as the number of people that the user follows.
-   Display all of the posts for that user, in reverse chronological order.
-   For any other user who is signed in, this page should also display a “Follow” or “Unfollow” button that will let the current user toggle whether or not they are following this user’s posts. Note that this only applies to any “other” user: a user should not be able to follow themselves.
#### Requirement 4
**Following**: The “Following” link in the navigation bar should take the user to a page where they see all posts made by users that the current user follows.

-   This page should behave just as the “All Posts” page does, just with a more limited set of posts.
-   This page should only be available to users who are signed in.
#### Requirement 5
**Pagination**: On any page that displays posts, posts should only be displayed 10 on a page. If there are more than ten posts, a “Next” button should appear to take the user to the next page of posts (which should be older than the current page of posts). If not on the first page, a “Previous” button should appear to take the user to the previous page of posts as well.

-   See the **Hints** section for some suggestions on how to implement this.
#### Requirement 6
**Edit Post**: Users should be able to click an “Edit” button or link on any of their own posts to edit that post.

-   When a user clicks “Edit” for one of their own posts, the content of their post should be replaced with a `textarea` where the user can edit the content of their post.
-   The user should then be able to “Save” the edited post. Using JavaScript, you should be able to achieve this without requiring a reload of the entire page.
-   For security, ensure that your application is designed such that it is not possible for a user, via any route, to edit another user’s posts.
#### Requirement 7
**“Like” and “Unlike”**: Users should be able to click a button or link on any post to toggle whether or not they “like” that post.

-   Using JavaScript, you should asynchronously let the server know to update the like count (as via a call to `fetch`) and then update the post’s like count displayed on the page, without requiring a reload of the entire page.

## Project Files

This project has 10 files that are:
 - static/network/
   - library.js
   - style.css
 - templates/network/
   - layout.html
   - index.html
   - post_form.html
   - following.html
   - login.html
   - profile.html
   - register.html
  - views.py
  - urls.py

###  Layout
In this file we have the template base where I used the Bootstrap Navbar components in addition to css and javascript.
In addition, the global variables below that take the values of the cookie and localStorage are declared.
````javascript
var  current_username = getCookie("username");
var  last_channel = localStorage.getItem('last_channel');
````
The excerpt below is responsible for listing the groups created according to [Requirement 3](#requirement-3).

### index
Displays the list of posts.
````python
<div id="posts">
    {% for post in posts %}
    <div class="card">
        <div class="card-body">
            <h5 class="card-title"><a href="{% url 'profile' post.user.username %}">{{post.user.username}}</a></h5>
            {%if post.user_id == user.id %}
            <a href="#" class="card-link" id="edit_link_{{post.id}}" data-id="{{post.id}}" data-toggle="modal">Edit</a>
            {%endif%}
            <p class="card-text" id="post_text_{{post.id}}"> {{ post.text }}</p>
            <form action="#" method="POST" id="frm_edit_{{post.id}}" data-id="{{post.id}}" style="display: none">
                {% csrf_token %}
                <div class="form-group">
                    {{ form_edit.id_post_edit_text }}
                </div>
                <div id="post_text_alert_{{post.id}}">
                </div>
                <div id="div_buttons">
                <button type="button" data-id="{{post.id}}" id="btn_close_{{post.id}}" class="btn btn-secondary"
                    data-dismiss="modal">Close</button>
                <button type="submit" class="btn btn-primary" id="btnSave">Save changes</button>
                </div>
            </form>
            <p class="card-text"><small class="text-muted">{{post.post_date}}</small></p>
            <p class="card-text">
                <div data-id="{{post.id}}"
                    class="card-link {% if post.current_like > 0 %} fas {%else%} far {% endif %} fa-heart">&nbsp<small
                        class="text-muted">{{post.like_set.count}}</small>
                        </div>
            </p>
        </div>
    </div>
    {% empty %}
    <h2>No posts</h2>
    {% endfor %}
</div>
````
### Post_form
Displays the form for creating new posts.
````python
{% if user.is_authenticated %}
<div class="card">
    <div class="card-body">
        <form action="{% url 'postmessage' %}" method="post">
            {% csrf_token %}
            <div class="form-group">
                <label for="post_text" class="h4">New Post</label>
                {{ form.post_text }}
            </div>
            <button type="submit" class="btn btn-primary btn-sm">Post</button>
        </form>
    </div>
</div>
{%endif%}
````
### following
Displays all user posts you follow.
````python
<h1>Following</h1>
<div id="posts">
    {% for post in posts %}
    <div class="card">
        <div class="card-body">
            <h5 class="card-title"><a href="{% url 'profile' post.user.username %}">{{post.user.username}}</a></h5>
            {%if post.user_id == user.id %}
            <a href="#" class="card-link" id="edit_link_{{post.id}}" data-id="{{post.id}}" data-toggle="modal">Edit</a>
            {%endif%}
            <p class="card-text" id="post_text_{{post.id}}"> {{ post.text }}</p>
            <form action="#" method="POST" id="frm_edit_{{post.id}}" data-id="{{post.id}}" style="display: none">
                {% csrf_token %}
                <div class="form-group">
                    {{ form_edit.id_post_edit_text }}
                </div>
                <div id="post_text_alert_{{post.id}}">
                </div>
                <button type="button" data-id="{{post.id}}" id="btn_close_{{post.id}}" class="btn btn-secondary"
                    data-dismiss="modal">Close</button>
                <button type="submit" class="btn btn-primary" id="btnSave">Save changes</button>
            </form>

            <p class="card-text"><small class="text-muted">{{post.post_date}}</small></p>
            <p class="card-text">
                <div data-id="{{post.id}}"
                    class="card-link {% if post.current_like > 0 %} fas {%else%} far {% endif %} fa-heart">&nbsp<small
                        class="text-muted">{{post.like_set.count}}</small></div>
            </p>
        </div>
    </div>
    {% empty %}
    <h2>No posts</h2>
    {% endfor %}
</div>
````
### profile
Displays a user's profile with all data and posts.
````python
<div class="card">
    <div class="card-body">
        <h1 class="card-title">{{user_profile.username}}</h1>
        <h5 class="card-text"><span id="sp_following">{{total_following}}</span> Following</h5>
        <h5 class="card-text"><span id="sp_followers">{{total_followers}}</span> Followers</h5>
        <p class="card-text"><small class="text-muted"></small></p>
        {% if user.is_authenticated and user.id != user_profile.id %}
        <p class="card-text">
        {% if is_following > 0 %}
            <button id="btnfollow" data-id="{{user_profile.id}}" type="button" class="btn btn-primary">Following</button>
        {% else %}
            <button id="btnfollow" data-id="{{user_profile.id}}" type="button" class="btn btn-outline-primary">Follow</button>
        {%endif%}
        </p>
        {%endif%}
    </div>
</div>
<div id="content">
    <h3>All Posts</h3>
    {% for post in posts %}
    <div class="card">
        <div class="card-body">
            <h5 class="card-title"><a href="{% url 'profile' post.user.username %}">{{post.user.username}}</a></h5>
            {%if post.user_id == user.id %}
            <a href="#" class="card-link" id="edit_link_{{post.id}}" data-id="{{post.id}}" data-toggle="modal">Edit</a>
            {%endif%}
            <p class="card-text" id="post_text_{{post.id}}"> {{ post.text }}</p>
            <form action="#" method="POST" id="frm_edit_{{post.id}}" data-id="{{post.id}}" style="display: none">
                {% csrf_token %}
                <div class="form-group">
                    {{ form_edit.id_post_edit_text }}
                </div>
                <div id="post_text_alert_{{post.id}}">
                </div>
                <div id="div_buttons">
                <button type="button" data-id="{{post.id}}" id="btn_close_{{post.id}}" class="btn btn-secondary"
                    data-dismiss="modal">Close</button>
                <button type="submit" class="btn btn-primary" id="btnSave">Save changes</button>
                </div>
            </form>

            <p class="card-text"><small class="text-muted">{{post.post_date}}</small></p>
            <p class="card-text">
                <div data-id="{{post.id}}"
                    class="card-link {% if post.current_like > 0 %} fas {%else%} far {% endif %} fa-heart">&nbsp<small
                        class="text-muted">{{post.like_set.count}}</small>
                        </div>
            </p>
        </div>
    </div>
    {% empty %}
    <h2>No posts</h2>
    {% endfor %}
</div>
````
### library
Below we have the contents of the library.js file with the commented code.
````javascript
 //Add the likeDislike () function call to the heart's onclick method
    document.querySelectorAll('.fa-heart').forEach(div => {
        div.onclick = function () {
            likeDislike(this);
        };
    });
    //Intercepts the submit of the post change form and sends an asynchronous request via javascript.
    document.querySelectorAll("[id^='frm_edit_']").forEach(form => {
        form.onsubmit = function (e) {
            e.preventDefault();
            this.querySelector('#div_buttons').style.display = "none";
            if (this.querySelector('#alert_message') != null) {
                this.querySelector('#alert_message').remove();
            }
            let alert = this.querySelector('#post_text_alert_' + this.dataset.id);

            let input = this.querySelector('div>textarea');
            if (input.value.trim().length == 0) {
                alertMessage({
                    'error': 'This field is required.'
                }, alert, this.dataset.id);
                this.querySelector('#div_buttons').style.display = "";
                return 0;
            }
            var formData = $(this).serialize();
            let csrftoken = this.querySelector("input[name='csrfmiddlewaretoken']").value;
            fetch(`/editpost/${this.dataset.id}`, {
                    method: 'POST',
                    headers: {
                        'Accept': 'text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,*/*;q=0.8',
                        'Content-Type': 'application/x-www-form-urlencoded',
                        "X-CSRFToken": csrftoken
                    },
                    body: formData
                })
                .then(response => response.json())
                .then(data => {

                    alertMessage(data, alert, this.dataset.id);
                    this.querySelector('#div_buttons').style.display = "";
                }).catch((error) => {
                    alertMessage({
                        'error': error.message
                    }, alert, this.dataset.id);
                    this.querySelector('#div_buttons').style.display = "";
                });
        }

    });
    //Displays the post editing form after clicking the edit link.
    document.querySelectorAll("[id^='edit_link_']").forEach(a => {
        a.onclick = function () {
            if (last_form != null) {
                hideForm(last_form);
            }
            last_form = this;
            let p = document.querySelector('#post_text_' + this.dataset.id);
            let form = document.querySelector('#frm_edit_' + this.dataset.id);
            p.style.display = 'none';
            form.querySelector('#id_post_edit_text').value = p.innerHTML;
            form.style.display = '';
        };

    });
    //Close the post editing form by clicking the close button.
    document.querySelectorAll("[id^='btn_close_']").forEach(a => {
        a.onclick = function () {
            hideForm(this);
        };

    });
    if (document.getElementById("btnfollow")) {
        document.querySelector('#btnfollow').addEventListener("click", function (event) {
            fetch(`/follow/${this.dataset.id}`)
                .then(response => response.json())
                .then(data => {
                    document.querySelector('#sp_followers').innerHTML = data.total_followers;
                    if (data.result == "follow") {
                        this.innerHTML = "Following";
                        this.className = "btn btn-primary";
                    } else {
                        this.innerHTML = "Follow";
                        this.className = "btn btn-outline-primary";
                    }
                });

        })

        //Displays the Unfollow text on the Following button when passing the mouser.
        document.querySelector('#btnfollow').addEventListener("mouseover", function (event) {
            if (this.className == "btn btn-primary") {
                this.innerHTML = "Unfollow"
            }
        });

        //Displays the text "Following" on the Following button when removing the mouser.
        document.querySelector('#btnfollow').addEventListener("mouseleave", function (event) {
            if (this.className == "btn btn-primary") {
                this.innerHTML = "Following"
            }
        });

    }
    //It receives an element and makes the asynchronous call of the like method.
    async function likeDislike(element) {
        await fetch(`/like/${element.dataset.id}`)
            .then(response => response.json())
            .then(data => {
                element.className = data.css_class;
                element.querySelector('small').innerHTML = data.total_likes;
            });
    }
    //Receive an element and hide the post editing form.
    function hideForm(element) {
        let p = document.querySelector('#post_text_' + element.dataset.id);
        let form = document.querySelector('#frm_edit_' + element.dataset.id);
        p.style.display = '';
        form.querySelector('#id_post_edit_text').value = p.innerHTML;
        form.style.display = 'none';
    }
    //Displays the alert message according to the return (success or error).
    function alertMessage(data, alert, id) {
        let div = document.createElement('div');
        let sucess = false;
        div.setAttribute('role', 'alert');
        div.setAttribute('id', 'alert_message');
        if (document.getElementById('alert_message') == null) {
            if (data.error) {
                if (data.error.id_post_edit_text) {
                    div.innerHTML = data.error.id_post_edit_text.join();
                } else {
                    div.innerHTML = data.error;
                }
                div.className = 'alert alert-dismissible fade alert-danger in show';
            } else {
                sucess = true;
                document.querySelector('#post_text_' + id).innerHTML = data.text;
                div.innerHTML = "Post changed successfully!";
                div.className = 'alert alert-dismissible fade alert-success in show';
            }
        }
        alert.appendChild(div);
        var alert_message = document.getElementById('alert_message');
        setTimeout(function () {
            if (alert_message != null) {
                $(alert_message).fadeOut("fast");
                alert_message.remove();
                if (sucess) {
                    document.querySelector('#frm_edit_' + id).style.display = 'none';
                    document.querySelector('#post_text_' + id).style.display = '';
                }
            }
        }, 1000);
    }
````
###  Views
This file contains all the site's backend rules, I will detail them below.

Initially we have the constant below that is with the value 10 to display only 10 posts per page meeting [Requirement 5](#requirement-5).

MAX_POSTS_PER_PAGE = 10

The index method below lists all posts from all users in order from most recent to oldest according to [Requirement 2](#requirement-2).
````python
def index(request):

    if request.user.is_authenticated:
        user = request.session['_auth_user_id']
        likes = Like.objects.filter(post=OuterRef('id'), user_id=user)
        posts = Post.objects.filter().order_by(
            '-post_date').annotate(current_like=Count(likes.values('id')))
    else:
        posts = Post.objects.order_by('-post_date').all()
````
The code snippet below performs pagination displaying 10 posts per page as per [Requirement 5](#requirement-5).
````python
    paginator = Paginator(posts, MAX_POSTS_PER_PAGE)
    page_number = request.GET.get('page')
    page_obj = paginator.get_page(page_number)
    return render(request, "network/index.html", {
        'posts': page_obj,
        'form': NewPostForm(),
        'form_edit': NewEditPostForm()
    })
  ````
  The following () function lists all user posts that the logged in user follows according to  [Requirement 4](#requirement-4).
  ````python
  def following(request):
    if request.user.is_authenticated:
        user = request.session['_auth_user_id']
        followers = Follower.objects.filter(follower=user)
        likes = Like.objects.filter(post=OuterRef('id'), user_id=user)
        posts = Post.objects.filter(user_id__in=followers.values('following_id')).order_by(
            '-post_date').annotate(current_like=Count(likes.values('id')))
    else:
        return HttpResponseRedirect(reverse("login"))

    paginator = Paginator(posts, MAX_POSTS_PER_PAGE)
    page_number = request.GET.get('page')
    page_obj = paginator.get_page(page_number)
    return render(request, "network/following.html", {
        'posts': page_obj,
        'form': NewPostForm()
    })
  ````
  The postmessage () function receives a message via post and writes it to the database as [Requirement 1](#requirement-1).
````python
def postmessage(request):
    if request.method == "POST":
        form = NewPostForm(request.POST)
        if form.is_valid():
            user = User.objects.get(id=request.session['_auth_user_id'])
            text = form.cleaned_data["post_text"]
            post = Post(user=user, text=text)
            post.save()
            return HttpResponseRedirect(reverse("index"))
    else:
        return HttpResponseRedirect(reverse("index"))
````
The editpost () function receives a post id asynchronously and changes the content of the post as per [Requirement 6](#requirement-6).
````python
def editpost(request, id):
    if request.is_ajax and request.method == "POST":
        form = NewEditPostForm(request.POST)
        if form.is_valid():
            text = form.cleaned_data["id_post_edit_text"]
            Post.objects.filter(
                id=id, user_id=request.session['_auth_user_id']).update(text=text)
            return JsonResponse({"result": 'ok', 'text': text})
        else:
            return JsonResponse({"error": form.errors}, status=400)

    return JsonResponse({"error": HttpResponseBadRequest("Bad Request: no like chosen")}, status=400)
````
The follow () function receives an id from a user so that the logged in user can follow or unfollow someone according to the [Requirement 3](#requirement-3).
````python
def follow(request, id):
    try:
        result = 'follow'
        user = User.objects.get(id=request.session['_auth_user_id'])
        user_follower = User.objects.get(id=id)
        follower = Follower.objects.get_or_create(
            follower=user, following=user_follower)
        if not follower[1]:
            Follower.objects.filter(
                follower=user, following=user_follower).delete()
            result = 'unfollow'
        total_followers = Follower.objects.filter(
            following=user_follower).count()
    except KeyError:
        return HttpResponseBadRequest("Bad Request: no like chosen")
    return JsonResponse({"result": result, "total_followers": total_followers})
````
The like () function is given a post id to like or unlike as per [Requirement 7](#requirement-7).
````python
def like(request, id):

    try:
        css_class = 'fas fa-heart'
        user = User.objects.get(id=request.session['_auth_user_id'])
        post = Post.objects.get(id=id)
        like = Like.objects.get_or_create(
            user=user, post=post)
        if not like[1]:
            css_class = 'far fa-heart'
            Like.objects.filter(user=user, post=post).delete()

        total_likes = Like.objects.filter(post=post).count()
    except KeyError:
        return HttpResponseBadRequest("Bad Request: no like chosen")
    return JsonResponse({
        "like": id, "css_class": css_class, "total_likes": total_likes
    })
````
The profile () function receives the username and displays a profile with the number of followers and followings in addition to all posts in reverse order from oldest to newest according to [Requirement 3](#requirement-3).
````python
def profile(request, username):
    is_following=0
    profile_user = User.objects.get(username=username)
    if request.user.is_authenticated:
        logged_user = request.session['_auth_user_id']
        is_following = Follower.objects.filter(
        follower=logged_user, following=profile_user).count()
        likes = Like.objects.filter(post=OuterRef('id'), user_id=logged_user)
        posts = Post.objects.filter(user=profile_user).order_by(
            'post_date').annotate(current_like=Count(likes.values('id')))
    else:
        posts = Post.objects.filter(
            user=profile_user).order_by('post_date').all()

    
    total_following = Follower.objects.filter(
        follower=profile_user).count()
    total_followers = Follower.objects.filter(
        following=profile_user).count()

    paginator = Paginator(posts, MAX_POSTS_PER_PAGE)
    page_number = request.GET.get('page')
    page_obj = paginator.get_page(page_number)
    return render(request, "network/profile.html", {
        "user_profile": profile_user, "posts": page_obj, "is_following": is_following, 'total_following': total_following, 'total_followers': total_followers, 'form': NewPostForm(), 'form_edit': NewEditPostForm()
    })
````

