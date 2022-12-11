//appsetting.json
{
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft": "Warning",
      "Microsoft.Hosting.Lifetime": "Information"
    }
  },
  "AllowedHosts": "*",
  "ConnectionStrings": {
    "MyConnection": "Server=KAVINDRA\\SQLEXPRESS;Database=UserName; User Id=SudoSu;Password=YourPassword;"
  }
}

//Model
using System.ComponentModel.DataAnnotations;
namespace Test.Models
{
    public class Employee
    {
        [Key]
        public int Id { get; set; }

        [Required(ErrorMessage = "FirstName Can't be Null")]
        public string FirstName { get; set; }
        public string LastName { get; set; }
        public string Gender { get; set; }

        [EmailAddress]
        [Required(ErrorMessage = "Please Enter a valid Email Address")]
        public string Email { get; set; }

        [Phone]
        [Required(ErrorMessage = "This field is mandatory")]
        public string PhoneNo { get; set; }
        public string Designation { get; set; }
        public int Salary { get; set; }
        public string Address { get; set; }
        public string Department { get; set; }
    }
}
//Dbcontext
using System;
using Microsoft.EntityFrameworkCore;
using Test.Models;
namespace Test.Database
{
    public class MyDbContext:DbContext
    {
        public MyDbContext(DbContextOptions<MyDbContext> options) : base(options)
        {

        }
        public DbSet<Employee> employees { get; set; }
    }
}
//startup.cs
using Microsoft.AspNetCore.Builder;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Hosting;
using System;
using Test.Database;
using Microsoft.EntityFrameworkCore;

namespace Test
{
    public class Startup
    {
        public Startup(IConfiguration configuration)
        {
            Configuration = configuration;
        }

        public IConfiguration Configuration { get; }

        // This method gets called by the runtime. Use this method to add services to the container.
        public void ConfigureServices(IServiceCollection services)
        {
            services.AddControllersWithViews();
            services.AddDbContext<MyDbContext>(options => options.
                UseSqlServer(Configuration.GetConnectionString("MyConnection")));
        }

        // This method gets called by the runtime. Use this method to configure the HTTP request pipeline.
        public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
        {
            if (env.IsDevelopment())
            {
                app.UseDeveloperExceptionPage();
            }
            else
            {
                app.UseExceptionHandler("/Home/Error");
            }
            app.UseStaticFiles();

            app.UseRouting();

            app.UseAuthorization();

            app.UseEndpoints(endpoints =>
            {
                endpoints.MapControllerRoute(
                    name: "default",
                    pattern: "{controller=AjaxAdmin}/{action=Index}/{id?}");
            });
        }
    }
}
//Controller
using System;
﻿using Microsoft.AspNetCore.Mvc;
using System.Collections.Generic;
using System.Linq;
using Test.Database;
using Test.Models;

namespace WebApi.Controllers
{
    public class AjaxAdminController : Controller
    {
        //issues-exclude time from DateTime
        //radio selection not working

        private readonly MyDbContext db;
        public AjaxAdminController(MyDbContext db)
        {
            this.db = db;
        }
        public IActionResult Index()
        {
            return View();
        }

        public JsonResult GetEmpData()
        {
            var data = db.employees.ToList();
            return Json(data);
        }
        [HttpGet]
        public IActionResult Insert()
        {
            ViewBag.CheckButton = "Insert";
            return View();
        }
        [HttpPost]
        [ValidateAntiForgeryToken]
        public IActionResult Insert(Employee model)
        {
            if (model.Id == 0)
            {
                db.employees.Add(model);
            }
            db.SaveChanges();
            ViewBag.Message = "Data saved SuccessFully";
            return View();
        }
        //for inserting data of popup form
        [HttpPost]
        public JsonResult InsertJqData(Employee model)
        {
            var emp = new Employee
            {
                Id = model.Id,
                FirstName = model.FirstName,
                LastName = model.LastName,
                Gender = model.Gender,
                Email = model.Email,
                PhoneNo = model.PhoneNo,
                Address = model.Address,
                Designation = model.Designation,
                Salary = model.Salary,
                Department = model.Department
            };
            if (emp.Id == 0)
            {
                db.employees.Add(emp);
            }
            else
            {
                db.employees.Update(emp);
            }
            db.SaveChanges();
            return new JsonResult("Done");
        }

        [HttpGet]
        public JsonResult Delete(int id)
        {
            var item = db.employees.Where(m => m.Id == id).FirstOrDefault();
            db.employees.Remove(item);
            db.SaveChanges();
            return Json("Ok");
        }
        [HttpGet]
        public JsonResult Edit(int id)
        {
            var item = db.employees.Where(m => m.Id == id).FirstOrDefault();
            return new JsonResult(item);
        }
    }
}
//View
@{
    ViewBag.title = "Jquery Operastions";
}
<script src="~/lib/jquery/dist/jquery.js"></script>
<script src="~/lib/jquery/dist/jquery.min.js"></script>

<button class="btn btn-sm btn-primary" id="btnAddEmployee">CreateNew</button>
<style>
    .elemnt-pos {
        margin-right: 100px;
    }
    .mandatory-fields{
        color:red;
        font-weight:bold;
    }
</style>
<table class="table table-bordered" id="Emp-Data">
    <thead>
        <tr>
            <th>Id</th>
            <th>FirstName</th>
            <th>LastName</th>
            <th>Gender</th>
            <th>Email</th>
            <th>PhoneNo</th>
            <th>Address</th>
            <th>Designation</th>
            <th>Salary</th>
            <th>Department</th>
            <th>Update</th>
            <th>Delete</th>
        </tr>
    </thead>
    <tbody id="table-data"></tbody>
</table>


<div class="modal fade bd-example-modal-lg" id="EmployeeModel">
    <div class="modal-dialog modal-lg">
        <div style="padding-left:100px;" class="modal-content">
            <div class="modal-header">
                <center>
                    <h3 id="model-title"></h3>
                </center>
                <button class="close text-danger" onclick="ClearTextBox()" data-dismiss="modal">&times;</button>
            </div>
            <div class="modal-body">
                <form method="post">
                    <div class="row">
                        <div class="form-group elemnt-pos">
                            <lable>Id</lable>
                            <input type="text" id="Id" class="form-control" disabled autocomplete="off" />

                        </div>
                        <div class="form-group">
                            <lable><span class="mandatory-fields">*</span>FirstName</lable>
                            <input type="text" id="FirstName" class="form-control" autocomplete="off" required="required" />

                        </div>
                    </div>
                    <br />
                    <div class="row">
                        <div class="form-group elemnt-pos">
                            <lable><span class="mandatory-fields">*</span>LastName</lable>
                            <input type="text" id="LastName" class="form-control" autocomplete="off" />
                        </div>

                        <div class="form-group">
                            <label><span class="mandatory-fields">*</span>Gender</label>
                            <select id="Gender" class="form-control">
                                <option value="Male">Male</option>
                                <option value="Female">Female</option>
                                <option value="Other">Other</option>
                            </select>
                        </div>
                    </div>
                    <br />
                    <div class="row">
                        <div class="form-group elemnt-pos">
                            <lable><span class="mandatory-fields">*</span>Email</lable>
                            <input type="text" id="Email" class="form-control" autocomplete="off" />
                        </div>
                        <div class="form-group">
                            <lable><span class="mandatory-fields">*</span>PhoneNo</lable>
                            <input type="text" id="PhoneNo" class="form-control" autocomplete="off" />
                        </div>
                    </div>
                    <br />
                    <div class=" row">
                        <div class="form-group elemnt-pos">
                            <lable>Address</lable>
                            <input type="text" id="Address" class="form-control" autocomplete="off" />
                        </div>
                        <div class="form-group">
                            <label>Designation</label>
                            <select id="Designation" class="form-control">
                                <option value="DotNet Developer">DotNet Developer</option>
                                <option value="HR">HR</option>
                                <option value="QA Engineer">QA Engineer</option>
                                <option value="Pentester">Pentester</option>
                                <option value="Full stack developer">Full stack developer</option>
                            </select>
                        </div>
                    </div>
                    <br />
                    <div class="row">
                        <div class="form-group elemnt-pos">
                            <lable>Salary</lable>
                            <input type="text" id="Salary" class="form-control " autocomplete="off" />
                        </div>
                        <div class="form-group">
                            <label>Department</label>
                            <select id="Department" class="form-control ">
                                <option value="HRA">HRA</option>
                                <option value="IT">IT</option>
                                <option value="Sales">Sales</option>
                                <option value="Management">Management</option>
                                <option value="Production support">Production support</option>
                            </select>
                        </div>
                    </div>
                    <br />

                </form>
            </div>
            <div style="color:red;font-weight:bold;">
                <span>Note: All * marked fileds are mandatory to fill.</span>
            </div>
            <div class="modal-footer">
                <button class="btn btn-primary" id="AddEmployee" onclick="InsertRecord()">Save</button>
                <button class="btn btn-warning" id="UpdateEmployee" style="display:none;" onclick="InsertRecord()">Update</button>
                <button class="btn btn-danger" onclick="ClearTextBox()" data-dismiss="modal">Close</button>
            </div>

        </div>
    </div>
</div>

<script>
    $(document).ready(function () {
        alert('Welcome to Index');
        GetMyEmpData();
    })
    //showing popup of insert form
    $('#btnAddEmployee').click(function () {
        $('#model-title').text("Add Employee Record");
       
        $('#EmployeeModel').modal('show');
       $('#AddEmployee').show();
        $('#UpdateEmployee').hide();
    })
    //function to post data to controller
    function InsertRecord() {
       // debugger
        var res = validate();
        if (res == false) {
            return false;
        }
        else {
            return true;
        }
        var data = {
            Id: $('#Id').val(),
            FirstName: $('#FirstName').val(),
            LastName: $('#LastName').val(),
            Gender: $('#Gender').val(),
            Email: $('#Email').val(),
            PhoneNo: $('#PhoneNo').val(),
            Address: $('#Address').val(),
            Designation: $('#Designation').val(),
            Salary: $('#Salary').val(),
            Department: $('#Department').val()
        };
        var check = $('#Id').val();
        $.ajax({
            url: '/AjaxAdmin/InsertJqData',
            data: data,
            type: 'POST',
            contentType:'application/x-www-form-urlencoded; charset=UTF-8',
            dataType: 'json',
            success: function () {
                if (check == 0) {
                    alert('Data Saved SuccessFully');
                }
                else {
                    alert('Data Updated SuccessFully');
                }
                //func call to hide popup
                ClearTextBox();
                HideModalPopUp();
                GetMyEmpData();
            },
            error: function () {
                alert('Something went wrong');
            }
        })
    }


    function GetMyEmpData() {
        //alert('GetData');
        $.ajax({
            url: '/AjaxAdmin/GetEmpData',
            type: 'Get',
            dataType: 'json',
            contentType: 'application/json;charset=utf-8;',

            success: function (result, status, xhr) {
                //alert(result);
                var row = '';
                $.each(result, function (index, item) {
                    row += "<tr>";
                    row += "<td>" + item.id + "</td>";
                    row += "<td>" + item.firstName + "</td>";
                    row += "<td>" + item.lastName + "</td>";
                    row += "<td>" + item.gender + "</td>";
                    row += "<td>" + item.email + "</td>";
                    row += "<td>" + item.phoneNo + "</td>";
                    row += "<td>" + item.address + "</td>";
                    row += "<td>" + item.designation + "</td>";
                    row += "<td>" + item.salary + "</td>";
                    row += "<td>" + item.department + "</td>";
                    @*row += '<td><button class="btn  btn-warning" asp-action="Insert" asp-controller="AjaxAdmin">Edit</button></td>';
                    <a class="btn  btn-success" asp-action="Insert" asp-controller="AjaxAdmin" >CreateNew</a>
                    @*row += "<td><a Id='DeleteBtn' asp-action='Delete' asp-controller='AjaxAdmin' asp-route-Id='"+item.Id+"'  class ='btn btn-danger'>Delete</a></td>";*@
                    row += '<td><a class="btn btn-warning" onclick="EditPopulate(' + item.id + ')">Edit</a></td>';
                    row += '<td><a class="btn btn-danger" onclick="Delete('+item.id+')">Delete</a></td>';
                });
                $('#table-data').html(row);
            },
            error: function () {
                alert("Something went wrong!!");
            }
        });
    }
    function Delete(id) {
       // debugger
        if (confirm('Are you sure,You want to delete the  record?')) {
            $.ajax({
                url: '/AjaxAdmin/Delete?id=' + id,
                success: function () {
                    alert("Record Deleted successfully!!");
                    GetMyEmpData();
                },
                error: function () {
                    alert('Invalid Request');
                }
            })
        }

    }
    function EditPopulate(id) {

        $.ajax({
            url: '/AjaxAdmin/Edit?id=' + id,
            type: 'GET',
            contentType: 'application/json;charset=utf-8;',

            success: function (response) {
                $('#model-title').text('Update Employee Record');
                $('#EmployeeModel').modal('show');
                $('#Id').val(response.id);
                $('#FirstName').val(response.firstName);
                $('#LastName').val(response.lastName);
                $('#Gender').val(response.gender);
                $('#Email').val(response.email);
                $('#PhoneNo').val(response.phoneNo);
                $('#Address').val(response.address);
                $('#Designation').val(response.designation);
                $('#Salary').val(response.salary);
                $('#Department').val(response.department);
                //$('#AddEmployee').css('display', 'none');
               // $('#UpdateEmployee').css('display', 'block');
                $('#AddEmployee').hide();
                $('#UpdateEmployee').show();

            },
            error: function () {
                alert('Data could not found!!');
            }
        })

    }
    function HideModalPopUp() {
        $('#EmployeeModel').modal('hide');
    }
    function ClearTextBox() {
        $('#Id').val('');
        $('#FirstName').val('');
        $('#LastName').val('');
        $('#Gender').val('');
        $('#Email').val('');
        $('#PhoneNo').val('');
        $('#Address').val('');
        $('#Designation').val('');
        $('#Salary').val('');
        $('#Department').val('');
    }
    function validate() {
        var isValid = true;
        if ($('#FirstName').val() == "") {
            $('#FirstName').css('border-color', 'Red');
            isValid = false;
        }
        else {
            $('#FirstName').css('border-color', 'lightgrey');
        }
        if ($('#LastName').val() == "") {
            $('#LastName').css('border-color', 'Red');
            isValid = false;
        }
        else {
            $('#LastName').css('border-color', 'lightgrey');
        }
        if ($('#Email').val() == "") {
            $('#Email').css('border-color', 'Red');
            isValid = false;
        }
        else {
            $('#Email').css('border-color', 'lightgrey');
        }
        if ($('#PhoneNo').val() == "") {
            $('#PhoneNo').css('border-color', 'Red');
            isValid = false;
        }
        else {
            $('#PhoneNo').css('border-color', 'lightgrey');
        }
        if ($('#Gender').val()== "") {
            $('#Gender').css('border-color', 'Red');
            isValid = false;
        }
        else {
            $('#Gender').css('border-color', 'lightgrey');
        }
        return isValid;
    }

</script>
