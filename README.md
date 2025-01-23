# Live Project
## Introduction
As part of my last two weeks at The Tech Academy, I participated in a two week sprint with other students and instructors to develop a C# MVC Web Application. It was a great opportunity to work on legacy code that has had many contributors. During the two weeks we had a sprint planning meeting, daily standups, and code retrospectives. The web application was for a local theater company to manage studio rentals, current productions, and host a blog. I worked in the productions area completing several stories in the [back end](#back-end-stories) as well as the [front end](#front-end-stories).

You will find some brief overviews of the stories that I worked on with several code snippets of important blocks of code along with some images.

## Back End Stories
- [Entity Model](#entity-model)
- [Search Bar](#search-bar)

### Entity Model
One of the first things I needed to do was create an Entity Model for our productions using Entity Framework. The code below was added to the Models folder.

```
namespace TheatreCMS3.Areas.Prod.Models
{
    public class Production
    {
        public int ProductionId { get; set; }
        public string Title { get; set; }
        public string Description { get; set; }
        public string Playwright { get; set; }
        public int Runtime { get; set; }
        public DateTime OpeningDay { get; set; }
        public DateTime ClosingDay { get; set; }
        
        //[DataType(DataType.Date)]
        public DateTime ShowTimeEve { get; set; }
        public DateTime? ShowTimeMat { get; set; }
        public int Season { get; set; }
        public bool IsWorldPremiere { get; set; }
        public string TicketLink { get; set; }

        public bool IsCurrentlyShowing { get; set; }
    }
}
```
After setting up the DbContext I added a Controller to setup CRUD functionality for the Production Model:

```
namespace TheatreCMS3.Areas.Prod.Controllers
{
    public class ProductionsController : Controller
    {
        private ApplicationDbContext db = new ApplicationDbContext();
        // GET: Prod/Productions
        public ActionResult Index()
        {
            return View(db.Productions.ToList());
        }

        // GET: Prod/Productions/Details/5
        public ActionResult Details(int? id)
        {
            if (id == null)
            {
                return new HttpStatusCodeResult(HttpStatusCode.BadRequest);
            }
            Production production = db.Productions.Find(id);
            if (production == null)
            {
                return HttpNotFound();
            }
            return View(production);
        }

        // GET: Prod/Productions/Create
        public ActionResult Create()
        {
            return View();
        }
        // POST: Prod/Productions/Create
        // To protect from overposting attacks, enable the specific properties you want to bind to, for 
        // more details see https://go.microsoft.com/fwlink/?LinkId=317598.
        [HttpPost]
        [ValidateAntiForgeryToken]
        public ActionResult Create([Bind(Include = "ProductionId,Title,Description,Playwright,Runtime,OpeningDay,ClosingDay,ShowTimeEve,ShowTimeMat,Season,IsWorldPremiere,TicketLink,IsCurrentlyShowing")] Production production)
        {
            if (ModelState.IsValid)
            {
                db.Productions.Add(production);
                db.SaveChanges();
                return RedirectToAction("Index");
            }
            return View(production);
        }

//Continued with Edit and Delete Functions.. 
```

### Search Bar
I added a search bar to quickly find a production from the Index page and only display matching Productions. To accomplish this I needed to update the Productions Controller Index Action.

```
namespace TheatreCMS3.Areas.Prod.Controllers
{
    public class ProductionsController : Controller
    {
        private ApplicationDbContext db = new ApplicationDbContext();

        // GET: Prod/Productions
        public ViewResult Index(string searchString)
        {
            var productions = from p in db.Productions select p;  

            if (!String.IsNullOrEmpty(searchString))
            {
                productions = productions.Where(p => p.Title.Contains(searchString));
            }
            return View(productions.ToList());
        }
    //....
    }
}
```
The Index.cshtml View needed to be modified as well:
```
@model IEnumerable<TheatreCMS3.Areas.Prod.Models.Production>
@{
    ViewBag.Title = "Index";
    Layout = "~/Views/Shared/_Layout.cshtml";
}

<link rel="stylesheet" type="text/css" href="~/Content/Areas/Prod.css" />

<h2>Productions</h2>

<p>
    @Html.ActionLink("Create New", "Create", null, new { @class = "btn btn-default cms-bg-secondary create-edit-button" })
</p>

//This block added our search bar.
@using (Html.BeginForm())
{
    <p class="flex">
        Search: <span class="border-color">@Html.TextBox("SearchString")</span>
        <input type="submit" value="Search" class="btn btn-sm cms-bg-secondary ml-2 create-edit-button" />
    </p>
}
//...
```
*Jump to:* [Introduction](#introduction), [Back End Stories](#back-end-stories), [Front End Stories](#front-end-stories), [Gallery](#gallery), [Conclusion](#conclusion)

## Front End Stories
- [Styled Create and Edit Pages](#styled-create-and-edit-pages)
- [Display Cards](#display-cards)

### Styled Create and Edit Pages
The Create and Edit pages needed to be properly styled to match the theme of the website. The Entity Framework default links were changed to buttons and flexbox was used to position the check boxes and buttons.
```
@model TheatreCMS3.Areas.Prod.Models.Production

@{
    ViewBag.Title = "Create";
    Layout = "~/Views/Shared/_Layout.cshtml";
}

<link rel="stylesheet" type="text/css" href="~/Content/Areas/Prod.css" />

@using (Html.BeginForm()) 
{
    @Html.AntiForgeryToken()
 
<div class="container-fluid cms-bg-secondary p-4 mt-4 container-radius">

    <div class="form-vertical container-flex m-2 p-2 container-radius cms-bg-main">
        <h3 class="mt-2 ml-3 broadway-font">Create Production</h3>
        <hr />
        @Html.ValidationSummary(true, "", new { @class = "text-primary" })

        <div class="form-group">
            @Html.LabelFor(model => model.Title, htmlAttributes: new { @class = "control-label col-md-2" })
            <div class="col-md-12">
                @Html.EditorFor(model => model.Title, new { htmlAttributes = new { @class = "form-control border-color", @placeholder = "What is the name of the play?", required = "" } })
                @Html.ValidationMessageFor(model => model.Title, "", new { @class = "text-primary" })
            </div>
        </div>

        <div class="form-group">
            @Html.LabelFor(model => model.Description, htmlAttributes: new { @class = "control-label col-md-2" })
            <div class="col-md-12">
                @Html.EditorFor(model => model.Description, new { htmlAttributes = new { @class = "form-control border-color", @placeholder = "Please enter a brief description"} })
                @Html.ValidationMessageFor(model => model.Description, "", new { @class = "text-primary" })
            </div>
        </div>

        //Continued for the rest of the Model Properties

        <div class="form-group d-flex btn-group justify-content-center">
            <div>
                @Html.ActionLink("Back to List", "Index", null, new { @class = "btn btn-lg bg-dark text-white" })
                <input type="submit" value="Create" class="btn btn-lg cms-bg-secondary ml-3 create-edit-button" />
            </div>
        </div>

    </div>
</div>
}
    @section Scripts {
        @Scripts.Render("~/bundles/jqueryval")
    }

```
The Edit page was modified in the same way.

### Display Cards
When the Entity Model was first created the production items were arranged in a list that was visually unappealing and did not match the theme of the website. I was tasked with making all created productions display on the Index page as cards. When clicked you would be directed to the details page. Hovering your mouse over the cards also revealed an Edit and Delete button that would take you to the appropriate pages.
```
@model IEnumerable<TheatreCMS3.Areas.Prod.Models.Production>
@{
    ViewBag.Title = "Index";
    Layout = "~/Views/Shared/_Layout.cshtml";
}

<link rel="stylesheet" type="text/css" href="~/Content/Areas/Prod.css" />

<h2>Productions</h2>

<p>
    @Html.ActionLink("Create New", "Create", null, new { @class = "btn btn-default cms-bg-secondary create-edit-button" })
</p>

<div class="container">
    <div class="row justify-content-center">
        @foreach (var item in Model)
        {
            <div class="card card-group bg-dark m-2 col-sm-auto text-center shadow" style="width: 16rem; height: 18rem;">             
                
                <img src="~/Content/images/theater.jpg" class="card-img-top prod-index-card-img" alt="No picture in ProductionPhotos" /><!--No ProductionsPhoto Model. Using sample image -->
                
                <div class="card-img-overlay d-inline-flex prod-overlay-pill">
                    <h3 class="ml-auto mr-1">
                        @Html.ActionLink("Edit", "Edit", new { id = item.ProductionId }, new { @class = "badge badge-pill cms-bg-secondary cms-text-light card-stretchlink-text" })
                    </h3>
                    <h3 class="mr-auto ml-1">
                        @Html.ActionLink("Delete", "Delete", new { id = item.ProductionId }, new { @class = "badge badge-pill cms-bg-main cms-text-light card-stretchlink-text" })
                    </h3>
                </div>

                <div class="card-body">
                    <h5 class="card-title card-stretchlink-text production-card-title-overflow my-auto">
                        @Html.ActionLink(item.Title, "Details", new { id = item.ProductionId }, new { @class = "stretched-link cms-text-light card-stretchlink-text text-decoration-none" })
                    </h5>
                </div>
            </div>
        }
    </div>
</div>

```
Bootstrap and a custom CSS file was used to accomplish the card display.

*Jump to:* [Introduction](#introduction), [Back End Stories](#back-end-stories), [Front End Stories](#front-end-stories), [Gallery](#gallery), [Conclusion](#conclusion)

## Gallery

![Productions_page](https://github.com/user-attachments/assets/982cf475-ae5c-4e1c-a6f1-2af4cd6123a3)

![Create_page](https://github.com/user-attachments/assets/d5012640-c6b0-46af-bc64-7ebef60729b5)

## Conclusion
During the course of my Live Project, I learned valuable skills as a developer and a problem solver. I enjoyed the collaboration and team aspect of the daily standups. I found myself messaging with the other developers to see what they were working on and what their strategies were as well as telling them about what I was working on. It was a great exercise in communication. During the daily standups we were all required to report our progress, our needs, and our plan for the day. The instructors and other developers were very helpful and it was clearly a team-oriented group. It was a great exercise in collaboration and problem solving.

*Jump to:* [Introduction](#introduction), [Back End Stories](#back-end-stories), [Front End Stories](#front-end-stories), [Gallery](#gallery)
