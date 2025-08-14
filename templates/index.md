---
layout: page
title: templates
---

<div class="articles-layout">
<div class="articles-main-content" markdown="1">

View and download templates grouped by technology vendors. Filter by practices like Cyber, Cloud, and AI.

{% assign providers = site.data.templates | group_by: 'Provider' | sort: 'name' %}
{% for provider_group in providers %}
  <div class="provider-heading">{{ provider_group.name }}</div>
  <table>
    <thead>
      <tr>
        <th>Category</th>
        <th>Solution Name</th>
        <th>Description</th>
        <th>Pre Sales</th>
        <th>Delivery</th>
        <th>Status</th>
      </tr>
    </thead>
    <tbody>
      {% for template in provider_group.items %}
      <tr>
        <td>{{ template.Category }}</td>
        <td>{{ template['Solution Name'] }}</td>
        <td>{{ template.Description }}</td>
        <td><a href="{{ template['Pre Sales Templates'] }}" target="_blank">templates</a></td>
        <td><a href="{{ template['Delivery Templates'] }}" target="_blank">templates</a></td>
        <td>{{ template.Status }}</td>
      </tr>
      {% endfor %}
    </tbody>
  </table>
{% endfor %}

</div>

<div class="articles-sidebar">
  <div class="sidebar-section">
    <h3>filter by provider</h3>
    <div class="related-links-list">
      <a href="#" class="year-filter-link provider-filter" data-provider="all">All Providers</a>
      {% assign providers = site.data.templates | group_by: 'Provider' | sort: 'name' %}
      {% for provider_group in providers %}
      <a href="#" class="year-filter-link provider-filter" data-provider="{{ provider_group.name }}">{{ provider_group.name }}</a>
      {% endfor %}
    </div>
  </div>
  
  <div class="sidebar-section">
    <h3>filter by category</h3>
    <div class="related-links-list">
      <a href="#" class="year-filter-link category-filter" data-category="all">All Categories</a>
      {% assign categories = site.data.templates | group_by: 'Category' | sort: 'name' %}
      {% for category_group in categories %}
      <a href="#" class="year-filter-link category-filter" data-category="{{ category_group.name }}">{{ category_group.name }}</a>
      {% endfor %}
    </div>
  </div>
</div>
</div>

<script>
document.addEventListener('DOMContentLoaded', function() {
  const providerFilters = document.querySelectorAll('.provider-filter');
  const categoryFilters = document.querySelectorAll('.category-filter');
  const providerSections = document.querySelectorAll('.provider-heading, table');
  
  let selectedProvider = 'all';
  let selectedCategory = 'all';
  
  // Set default active filters
  const allProviderFilter = document.querySelector('.provider-filter[data-provider="all"]');
  const allCategoryFilter = document.querySelector('.category-filter[data-category="all"]');
  if (allProviderFilter) allProviderFilter.classList.add('active');
  if (allCategoryFilter) allCategoryFilter.classList.add('active');
  
  // Provider filter functionality
  providerFilters.forEach(filter => {
    filter.addEventListener('click', function(e) {
      e.preventDefault();
      
      providerFilters.forEach(f => f.classList.remove('active'));
      this.classList.add('active');
      selectedProvider = this.getAttribute('data-provider');
      applyFilters();
    });
  });
  
  // Category filter functionality
  categoryFilters.forEach(filter => {
    filter.addEventListener('click', function(e) {
      e.preventDefault();
      
      categoryFilters.forEach(f => f.classList.remove('active'));
      this.classList.add('active');
      selectedCategory = this.getAttribute('data-category');
      applyFilters();
    });
  });
  
  function applyFilters() {
    providerSections.forEach(section => {
      const isProviderHeading = section.classList.contains('provider-heading');
      const isTable = section.tagName === 'TABLE';
      
      if (isProviderHeading) {
        const providerName = section.textContent.trim();
        const showProvider = selectedProvider === 'all' || providerName === selectedProvider;
        
        if (showProvider) {
          // Check if table should be shown based on category filter
          const table = section.nextElementSibling;
          if (table && table.tagName === 'TABLE') {
            if (selectedCategory === 'all') {
              section.style.display = 'block';
              table.style.display = 'block';
              // Show all rows
              const rows = table.querySelectorAll('tbody tr');
              rows.forEach(row => row.style.display = 'table-row');
            } else {
              // Check if table has any rows matching the category
              const rows = table.querySelectorAll('tbody tr');
              let hasMatchingRows = false;
              rows.forEach(row => {
                const categoryCell = row.children[0]; // First column is category
                if (categoryCell && categoryCell.textContent.trim() === selectedCategory) {
                  row.style.display = 'table-row';
                  hasMatchingRows = true;
                } else {
                  row.style.display = 'none';
                }
              });
              // Only show provider heading and table if there are matching rows
              if (hasMatchingRows) {
                section.style.display = 'block';
                table.style.display = 'block';
              } else {
                section.style.display = 'none';
                table.style.display = 'none';
              }
            }
          }
        } else {
          section.style.display = 'none';
          if (section.nextElementSibling && section.nextElementSibling.tagName === 'TABLE') {
            section.nextElementSibling.style.display = 'none';
          }
        }
      }
    });
  }
});
</script>