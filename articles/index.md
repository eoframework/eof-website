---
layout: page
title: articles
---

<div class="articles-layout">
  <div class="articles-main-content">
    <div style="text-align: right; margin-bottom: 0.25rem;">
      <a href="{{ "/feed.xml" | relative_url }}" target="_blank" style="color: #c00; text-decoration: none; font-size: 0.9em;">subscribe via RSS</a>
    </div>

    <div class="simplified-articles-list">
    {% for post in site.posts %}
      <div class="simplified-article-item" data-year="{{ post.date | date: "%Y" }}">
        <a href="{{ post.url | relative_url }}" class="simplified-article-title">- {{ post.title }}</a>
        <span class="simplified-article-date">{{ post.date | date: "%B %d, %Y" }}</span>
      </div>
    {% endfor %}
    </div>

    {% if site.posts.size == 0 %}
    <p>No articles available yet. Check back soon for the latest insights on the EO Framework.</p>
    {% endif %}
  </div>
  
  <div class="articles-sidebar">
    <div class="sidebar-section">
      <h3>filter by year</h3>
      <div class="year-filter-list">
        <a href="#" class="year-filter-link" data-year="all">All Articles</a>
        {% assign current_year = site.time | date: "%Y" | plus: 0 %}
        {% for i in (0..9) %}
          {% assign year = current_year | minus: i %}
          <a href="#" class="year-filter-link" data-year="{{ year }}">{{ year }}</a>
        {% endfor %}
      </div>
    </div>
  </div>
</div>

<script>
document.addEventListener('DOMContentLoaded', function() {
  const yearLinks = document.querySelectorAll('.year-filter-link');
  const articles = document.querySelectorAll('.simplified-article-item');
  const currentYear = new Date().getFullYear();
  
  // Remove active class from all links first
  yearLinks.forEach(l => l.classList.remove('active'));
  
  // Find and activate current year link
  const currentYearLink = document.querySelector(`.year-filter-link[data-year="${currentYear}"]`);
  
  if (currentYearLink) {
    currentYearLink.classList.add('active');
    filterByYear(currentYear.toString());
  } else {
    // Fallback: show all articles and highlight "All"
    const allLink = document.querySelector('.year-filter-link[data-year="all"]');
    if (allLink) {
      allLink.classList.add('active');
      filterByYear('all');
    }
  }
  
  yearLinks.forEach(link => {
    link.addEventListener('click', function(e) {
      e.preventDefault();
      
      // Remove active class from all links
      yearLinks.forEach(l => l.classList.remove('active'));
      
      // Add active class to clicked link
      this.classList.add('active');
      
      const selectedYear = this.getAttribute('data-year');
      filterByYear(selectedYear);
    });
  });
  
  function filterByYear(year) {
    articles.forEach(article => {
      if (year === 'all' || article.getAttribute('data-year') === year) {
        article.style.display = 'block';
      } else {
        article.style.display = 'none';
      }
    });
    
    // Check if any articles are visible
    const visibleArticles = Array.from(articles).filter(article => 
      article.style.display !== 'none'
    );
    
    // Show/hide no articles message
    let noArticlesMsg = document.querySelector('.no-articles-message');
    if (visibleArticles.length === 0) {
      if (!noArticlesMsg) {
        noArticlesMsg = document.createElement('p');
        noArticlesMsg.className = 'no-articles-message';
        noArticlesMsg.style.cssText = 'text-align: center; color: #666; margin: 2rem 0;';
        document.querySelector('.simplified-articles-list').appendChild(noArticlesMsg);
      }
      noArticlesMsg.textContent = `No articles found for ${year === 'all' ? 'the selected filter' : year}.`;
      noArticlesMsg.style.display = 'block';
    } else if (noArticlesMsg) {
      noArticlesMsg.style.display = 'none';
    }
  }
});
</script>