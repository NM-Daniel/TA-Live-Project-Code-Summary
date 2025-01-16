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

//Continued with Edit and Detail Functions.. 
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
*Jump to:* [Introduction](#introduction), [Back End Stories](#back-end-stories), [Front End Stories](#front-end-stories), [Conclusion](#conclusion)

## Front End Stories
- [Styled Donations Page](#styled-donations-page)
- [Styled Create and Edit Pages](#styled-create-and-edit-pages)
- [Display Cards](#display-cards)

### Styled Donations Page
### Styled Create and Edit Pages
### Display Cards

*Jump to:* [Introduction](#introduction), [Back End Stories](#back-end-stories), [Front End Stories](#front-end-stories), [Conclusion](#conclusion)

## Conclusion
During the course of my Live Project, I learned valuable skills as a developer and a problem solver. I enjoyed the collaboration and team aspect of the daily standups. I found myself messaging with the other developers to see what they were working on and what their strategies were as well as telling them about what I was working on. It was a great exercise in communication. During the daily standups we were all required to report our progress, our needs, and our plan for the day. The instructors and other developers were very helpful and it was clearly a team-oriented group.

*Jump to:* [Introduction](#introduction), [Back End Stories](#back-end-stories), [Front End Stories](#front-end-stories)
