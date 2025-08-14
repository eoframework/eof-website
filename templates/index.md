---
layout: page
title: templates
---

<div class="articles-layout">
<div class="articles-main-content" markdown="1">

View and download templates grouped by technology vendors. Filter by practices like Cyber, Cloud, and AI.

<div style="margin: 1rem 0; display: flex; justify-content: space-between; align-items: center;">
  <div></div>
  <div style="display: flex; align-items: center; gap: 1rem;">
    <div style="width: 200px; padding-right: 1rem;">
      <input type="text" id="search-input" placeholder="Search templates..." style="width: 100%; padding: 0.35rem 0.5rem; border: 1px solid #ddd; border-radius: 3px; font-size: 0.85em;">
    </div>
    <div id="filter-status" style="display: none; text-align: right; font-size: 0.85em; white-space: nowrap;">
      <span style="font-weight: 500; color: #333; padding-right: 0.25rem;">Active Filters:</span> <span id="active-filters"></span>
      <button id="clear-filters" style="margin-left: 1rem; padding: 0.25rem 0.5rem; background: transparent; color: #c00; border: none; border-radius: 3px; cursor: pointer; text-decoration: underline; font-size: 0.85em;">Clear All Filters</button>
    </div>
  </div>
</div>

<div id="no-match-message" style="margin: 1rem 0; padding: 0; text-align: center; color: #555; font-size: 1em; display: none;">
  No templates match the selected filter criteria.
</div>

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
  const searchInput = document.getElementById('search-input');
  
  let selectedProvider = 'all';
  let selectedCategory = 'all';
  let searchTerm = '';
  
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
  
  // Search functionality
  searchInput.addEventListener('input', function() {
    searchTerm = this.value.toLowerCase().trim();
    applyFilters();
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
              // Check if table has any rows matching the search term
              const rows = table.querySelectorAll('tbody tr');
              let hasMatchingRows = false;
              rows.forEach(row => {
                let searchMatch = true;
                if (searchTerm) {
                  const rowText = row.textContent.toLowerCase();
                  searchMatch = rowText.includes(searchTerm);
                }
                
                if (searchMatch) {
                  row.style.display = 'table-row';
                  hasMatchingRows = true;
                } else {
                  row.style.display = 'none';
                }
              });
              
              if (hasMatchingRows) {
                section.style.display = '';
                table.style.display = '';
              } else {
                section.style.display = 'none';
                table.style.display = 'none';
              }
            } else {
              // Check if table has any rows matching the category and search term
              const rows = table.querySelectorAll('tbody tr');
              let hasMatchingRows = false;
              rows.forEach(row => {
                const categoryCell = row.children[0]; // First column is category
                const categoryMatch = selectedCategory === 'all' || (categoryCell && categoryCell.textContent.trim() === selectedCategory);
                
                // Search across all text content in the row
                let searchMatch = true;
                if (searchTerm) {
                  const rowText = row.textContent.toLowerCase();
                  searchMatch = rowText.includes(searchTerm);
                }
                
                if (categoryMatch && searchMatch) {
                  row.style.display = 'table-row';
                  hasMatchingRows = true;
                } else {
                  row.style.display = 'none';
                }
              });
              // Only show provider heading and table if there are matching rows
              if (hasMatchingRows) {
                section.style.display = '';
                table.style.display = '';
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
    
    updateFilterStatus();
    checkForNoMatches();
  }
  
  function updateFilterStatus() {
    const filterStatus = document.getElementById('filter-status');
    const activeFilters = document.getElementById('active-filters');
    
    const filters = [];
    if (selectedProvider !== 'all') {
      filters.push(`<span style="color: #c00;">Provider:</span> ${selectedProvider}`);
    }
    if (selectedCategory !== 'all') {
      filters.push(`<span style="color: #c00;">Category:</span> ${selectedCategory}`);
    }
    if (searchTerm !== '') {
      filters.push(`<span style="color: #c00;">Search:</span> ${searchTerm}`);
    }
    
    if (filters.length > 0) {
      activeFilters.innerHTML = filters.join(', ');
      filterStatus.style.display = 'block';
    } else {
      filterStatus.style.display = 'none';
    }
  }
  
  function checkForNoMatches() {
    const noMatchMessage = document.getElementById('no-match-message');
    const visibleProviderHeadings = document.querySelectorAll('.provider-heading[style="display: block"], .provider-heading:not([style*="display: none"])');
    
    // Check if any provider sections are actually visible and have content
    let hasVisibleContent = false;
    visibleProviderHeadings.forEach(heading => {
      if (heading.style.display !== 'none') {
        const table = heading.nextElementSibling;
        if (table && table.tagName === 'TABLE' && table.style.display !== 'none') {
          const visibleRows = table.querySelectorAll('tbody tr:not([style*="display: none"])');
          if (visibleRows.length > 0) {
            hasVisibleContent = true;
          }
        }
      }
    });
    
    if (!hasVisibleContent && (selectedProvider !== 'all' || selectedCategory !== 'all' || searchTerm !== '')) {
      noMatchMessage.style.display = 'block';
    } else {
      noMatchMessage.style.display = 'none';
    }
  }
  
  // Clear filters functionality
  document.getElementById('clear-filters').addEventListener('click', function() {
    selectedProvider = 'all';
    selectedCategory = 'all';
    searchTerm = '';
    searchInput.value = '';
    
    // Reset filter link states
    providerFilters.forEach(f => f.classList.remove('active'));
    categoryFilters.forEach(f => f.classList.remove('active'));
    
    const allProviderFilter = document.querySelector('.provider-filter[data-provider="all"]');
    const allCategoryFilter = document.querySelector('.category-filter[data-category="all"]');
    if (allProviderFilter) allProviderFilter.classList.add('active');
    if (allCategoryFilter) allCategoryFilter.classList.add('active');
    
    applyFilters();
  });
});
</script>