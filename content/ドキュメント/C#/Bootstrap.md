+++
title = "Bootstrap"
weight = 1
+++

## index.cshtml
``` html
@{
    ViewData["Title"] = "Home Page";
}
@model List<WebApplication1.Models.User>
@using System.Text.Json

<div class="container">
    <h2>ユーザー一覧</h2>
    <div id="userTable"></div>
</div>

<script>
    const users = @Html.Raw(JsonSerializer.Serialize(Model));
</script>

@section Scripts {
    <script type="module" src="~/js/components/tableComponent.js"></script>
    <script type="module" src="~/js/pages/userList.js"></script>
}

```
## userList.ts
``` ts
import { BootstrapTable, ColumnDefinition } from "../components/tableComponent.js";

interface User {
    Id: number;
    Name: string;
    Email: string;
}

// Razorから埋め込み済みのグローバル変数を使う宣言
declare const users: User[];

window.addEventListener("DOMContentLoaded", () => {
    const columns: ColumnDefinition<User>[] = [
        { header: "ID", field: "Id" },
        { header: "NAME", field: "Name" },
        { header: "MAIL", field: "Email" }
    ];

    const table = new BootstrapTable<User>("userTable", columns);
    table.render(users);
});
```

## tableComponent.ts
``` ts
export interface ColumnDefinition<T> {
    header: string;
    field: keyof T;
}

export class BootstrapTable<T> {
    private container: HTMLElement;
    private columns: ColumnDefinition<T>[];

    constructor(containerId: string, columns: ColumnDefinition<T>[]) {
        const el = document.getElementById(containerId);
        if (!el) throw new Error(`Element #${containerId} not found`);
        this.container = el;
        this.columns = columns;
    }

    render(data: T[]) {
        let html = `<table class="table table-bordered table-striped">
                        <thead><tr>`;
        this.columns.forEach(col => {
            html += `<th>${col.header}</th>`;
        });
        html += `</tr></thead><tbody>`;

        data.forEach(item => {
            html += `<tr>`;
            this.columns.forEach(col => {
                console.log(item);
                console.log(col);
                console.log(col.field);
                console.log(item[col.field]);
                html += `<td>${(item[col.field] ?? '')}</td>`;
            });
            html += `</tr>`;
        });

        html += `</tbody></table>`;
        this.container.innerHTML = html;
    }
}
```

## HomeController.cs
``` cs
using System.Diagnostics;
using Microsoft.AspNetCore.Mvc;
using WebApplication1.Models;

namespace WebApplication1.Controllers
{
    public class HomeController : Controller
    {
        private readonly ILogger<HomeController> _logger;

        public HomeController(ILogger<HomeController> logger)
        {
            _logger = logger;
        }

        public IActionResult Index()
        {
            var users = new List<User>
            {
                new User { Id = 1, Name = "田中", Email = "tanaka@example.com" },
                new User { Id = 2, Name = "佐藤", Email = "sato@example.com" }
            };
            return View(users);
        }

        public IActionResult Privacy()
        {
            return View();
        }

        [ResponseCache(Duration = 0, Location = ResponseCacheLocation.None, NoStore = true)]
        public IActionResult Error()
        {
            return View(new ErrorViewModel { RequestId = Activity.Current?.Id ?? HttpContext.TraceIdentifier });
        }
    }
}
```