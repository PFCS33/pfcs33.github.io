---
layout: page
title: projects
permalink: /projects/
description:
nav: true
nav_order: 2
display_categories: [Work, Research, "Visual Computing Lab"]
horizontal: false
---

<!-- pages/projects.md -->
<div class="projects">
{% if site.enable_project_categories and page.display_categories %}
  <!-- Category filter bar -->
  <div class="project-filters">
    <button class="filter-btn active" data-category="all">All</button>
    {% for category in page.display_categories %}
      <button class="filter-btn" data-category="{{ category }}">{{ category }}</button>
    {% endfor %}
  </div>

  <!-- Display categorized projects -->

{% for category in page.display_categories %}

  <div class="project-category-group" data-group="{{ category }}">
  <a id="{{ category }}" href=".#{{ category }}">
    <h2 class="category">{{ category }}</h2>
  </a>
  {% assign categorized_projects = site.projects | where: "category", category %}
  {% assign sorted_projects = categorized_projects | sort: "importance" %}
  <!-- Generate cards for each project -->
  {% if page.horizontal %}
  <div class="container">
    <div class="row row-cols-1 row-cols-md-2">
    {% for project in sorted_projects %}
      {% include projects_horizontal.liquid %}
    {% endfor %}
    </div>
  </div>
  {% else %}
  <div class="grid">
    {% for project in sorted_projects %}
      {% include projects.liquid %}
    {% endfor %}
  </div>
  {% endif %}
  </div>
  {% endfor %}

  <script>
    document.addEventListener("DOMContentLoaded", function () {
      var buttons = document.querySelectorAll(".filter-btn");
      var groups = document.querySelectorAll(".project-category-group");

      buttons.forEach(function (btn) {
        btn.addEventListener("click", function () {
          buttons.forEach(function (b) { b.classList.remove("active"); });
          btn.classList.add("active");

          var cat = btn.getAttribute("data-category");
          groups.forEach(function (g) {
            if (cat === "all" || g.getAttribute("data-group") === cat) {
              g.style.display = "";
            } else {
              g.style.display = "none";
            }
          });
        });
      });
    });
  </script>

{% else %}

<!-- Display projects without categories -->

{% assign sorted_projects = site.projects | sort: "importance" %}

  <!-- Generate cards for each project -->

{% if page.horizontal %}

  <div class="container">
    <div class="row row-cols-1 row-cols-md-2">
    {% for project in sorted_projects %}
      {% include projects_horizontal.liquid %}
    {% endfor %}
    </div>
  </div>
  {% else %}
  <div class="grid">
    {% for project in sorted_projects %}
      {% include projects.liquid %}
    {% endfor %}
  </div>
  {% endif %}
{% endif %}
</div>
