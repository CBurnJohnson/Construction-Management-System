# Construction Management System Live Project

# Introduction

For the past two weeks I have spent my time working on a fresh Asp.net MVC web application that is designed to be a construction management system. Working on a new project had many great learning opportunities, for example needing to learn the new technologies that I would implement in the project. Some of these new technologies are Entity Framework Code First and SignalR. Since the project was brand new I had the chance to work on many [front end stories](#front-end-stories), which allowed me to create the foundation of the application’s style. Because fresh projects don't have much logic behind them I was able to work on a good amount of [back end stories](#back-end-stories) that I am very proud to have worked on. Over the two week sprint that I worked on this project I also had learned a lot of useful project management [skills](#other-skills-learned) that I will definitely continue to use in the future.

Below are some of the stories I worked on during the two week sprint with short descriptions, code snippets, pictures, and navigation links.



*Navigation: [Introduction](#introduction), [Back End Stories](#back-end-stories), [Front End Stories](#front-end-stories), [Other Skills](#other-skills-learned), [Top of Page](#construction-management-system-live-project)*

## Back End Stories

- [Seed Method](#seed-method)
- [User Creation](#user-creation)



*Navigation: [Introduction](#introduction), [Back End Stories](#back-end-stories), [Front End Stories](#front-end-stories), [Other Skills](#other-skills-learned), [Top of Page](#construction-management-system-live-project)*

### Seed Method

I was assigned to code a seed method that created a new role called “Admin”, and populated the database with test data. The “Admin” role helps with the user creation system.

```
// Create Admin Role
var roleStore = new RoleStore<IdentityRole>(context);
var roleManager = new RoleManager<IdentityRole>(roleStore);
var userStore = new UserStore<ApplicationUser>(context);
var userManager = new UserManager<ApplicationUser>(userStore);
 
var role = roleManager.FindByName("Admin");
if (role == null)
{
    role = new IdentityRole("Admin");
    roleManager.Create(role);
}
 
// Admin user test data 
 
var newAdmin = new ApplicationUser()
{
    Id = "Admin1",
    UserName = "AdminAccount1",
    PasswordHash = "AdminPassword",
    Email = "xxx@xxx.net",
    PhoneNumber = "555-123-4567",
};
userManager.Create(newAdmin);
userManager.AddToRole(newAdmin.Id, "Admin");
 
 
// Normal user test data
var users = new List<ApplicationUser>
{
    new ApplicationUser{UserName="Bob", Email="Bob@gmail.com", PasswordHash="Password", PhoneNumber="541-388-0923"},
    new ApplicationUser{UserName="Jim", Email="Jim@gmail.com", PasswordHash="Password", PhoneNumber="541-400-3421"},
    new ApplicationUser{UserName="Fred", Email="Fred@gmail.com", PasswordHash="Password", PhoneNumber="501-987-4321"},
};
users.ForEach(s => context.Users.Add(s));
```



### User Creation

Another story I worked on was to create how admins would register new users. For a new account to be created an admin account would have to make a user creation request. The Admin chooses a username and then a random 4 digit confirmation code for that user name will generate. The Admin would then give the information to the person that the Admin wanted to have create an account.

```
[HttpPost]
[ValidateAntiForgeryToken]
[Authorize(Roles = "Admin")]
public ActionResult Create([Bind(Include = "UserCreationRequestId,UserName")] UserCreationRequest userCreationRequest)
{
    int _min = 1000;
    int _max = 9999;
    var random = new Random();
    var randomConfirm = random.Next(_min, _max);
    if (ModelState.IsValid)
    {
        userCreationRequest.ConfirmationCode = randomConfirm;
        userCreationRequest.UserCreationRequestId = Guid.NewGuid();
        db.UserCreationRequests.Add(userCreationRequest);
        db.SaveChanges();
        return RedirectToAction("Details", new { id = userCreationRequest.UserCreationRequestId });
    }

​    return View(userCreationRequest)
```


The person wanting to create an account must have both the username and confirmation code to be prompted to create a new account. 

```
[HttpPost]
public ActionResult Confirmation(string userName, int confirmationCode)
{
    var allRequests = db.UserCreationRequests;
    var userRequests = new List<UserCreationRequest>();
    foreach (var request in allRequests)
    {
        if (request.UserName == userName && request.ConfirmationCode == confirmationCode)
        {
            return View("~/Views/Account/Register.cshtml");
        }
    }
    return View();
```



## Front End Stories

- [Username Creation Page](#username-creation-page)
- [Navigation Bar](#navigation-bar)



*Navigation: [Introduction](#introduction), [Back End Stories](#back-end-stories), [Front End Stories](#front-end-stories), [Other Skills](#other-skills-learned), [Top of Page](#construction-management-system-live-project)*

### Username Creation Page

One of the front end stories I worked on was creating the style for the username creation view.

![UsernameCreation](https://user-images.githubusercontent.com/44681780/54860213-45d6e600-4cd4-11e9-80a3-765cbea7f1f6.PNG)



### Navigation Bar

A portion of the project I had the chance to style the navigation bar and add a drop-down menu. When styling the navigation bar I ran into a number of obstacles with bootstrap overriding my custom styles, but I was able to persevere through and come up with unique solutions so my styling doesn't get overridden.

![NavBar](https://user-images.githubusercontent.com/44681780/54860239-820a4680-4cd4-11e9-93cf-76d4ec5e8148.PNG)



I also created a drop-down menu only accessible if you're logged into a admin account.

![AdminNavBar](https://user-images.githubusercontent.com/44681780/54860243-93535300-4cd4-11e9-8e8a-99d451a25c18.PNG)



```
@if (ViewContext.HttpContext.User.IsInRole("Admin"))
{
    <ul class="nav navbar-nav navbar-right">
        <li class="dropdown">
            <a class="dropdown-toggle" data-toggle="dropdown" href="#">Admin
            <span class="caret"></span></a>
            <ul class="dropdown-menu">
                <li>@Html.ActionLink("Add a New User", "Create", "UserCreationRequests")</li>
            </ul>
        </li>
    </ul>
}
```



Part of the story I was working on for the navigation bar was to implement a slide down animation. The slide down animation would happen when loading any of the applications pages. Coming into this task was very difficult for me since I haven't worked on many animation effects before, but after a little research I was able to use a little jQuery to help solve my problem. This code snippet also made a slide down effect on the drop-down menus in the navigation bar.

```
/* NavBar Animation */
 
$(function () {
    var nav = document.getElementById('animate-nav');
    nav.classList.toggle('shown');
});
 
// slideDown animation on dropdown menu

$('.dropdown').on('show.bs.dropdown', function () {
    $(this).find('.dropdown-menu').first().stop(true, true).slideDown();
});
 
// slideUp animation on dropdown menu

$('.dropdown').on('hide.bs.dropdown', function () {
    $(this).find('.dropdown-menu').first().stop(true, true).slideUp();
});
```



And the CSS that helped make this animation effect possible.

```
#animate-nav {
    margin-top: -200px;
    transition: margin-top 2s;
}
 
#animate-nav.shown {
    margin-top: 0px;
}
```



## Other Skills Learned

- Learned a lot about project work flow, and Agile project management systems like Scrum.
- I was the only person, other than the project director, on this project during the two week split so I became very self-sufficient in researching unfamiliar concepts and technologies.
- Gained new perspectives on how to problem solve while developing.



*Navigation: [Introduction](#introduction), [Back End Stories](#back-end-stories), [Front End Stories](#front-end-stories), [Other Skills](#other-skills-learned), [Top of Page](#construction-management-system-live-project)*