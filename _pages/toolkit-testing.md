---
layout: page
permalink: /toolkit-testing/
title: Toolkit Testing
description:
nav: true
nav_order: 5
---

## Explore the Toolkit

{% assign lesson_plans = site.cards | where: "title", "card testing" %}

{% for lesson_plan in lesson_plans %}
{% for value in lesson_plan.domain %}
{{ value }}
{% endfor %}
{% endfor %}

<div style="background-color: #f2f2f2; padding: 10px;">
  <div id="filter-options" style="font-size: 0.8em;">
    
    <label for="resource-filter">Type of Resource:</label>
    <select id="resource-filter">
      <option value="all">All</option>
      {% for resource in site.data.cards.resources %}
      <option value="{{ resource.name }}">{{ resource.name }}</option>
      {% endfor %}
    </select>

    <br>

    <label for="domain-filter">Primary Domain:</label>
    <select id="domain-filter">
      <option value="all">All</option>
      {% for domain in site.data.cards.domains %}
      <option value="{{ domain }}">{{ domain }}</option>
      {% endfor %}
    </select>

    <br>

    <label for="subdomain-filter">Subdomain:</label>
    <select id="subdomain-filter">
      <option value="all">All</option>
      {% for subdomain in site.data.cards.subdomains %}
      <option value="{{ subdomain }}">{{ subdomain }}</option>
      {% endfor %}
    </select>

    <br>

    <label for="search-input">Search:</label>
    <input type="text" id="search-input" style="width: 300px;" placeholder="Search by word, phrase, or keyword">
    <button id="search-button">Search</button>
    <button id="clear-search">Clear Search</button>

  </div>
</div>

{% assign cards = site.cards | sort: "title" %}

<div id="card-list" style="margin-top: 20px;">
  {% for card in cards %}
  {% assign resource = site.data.cards.resources | where: "name", card.resource | first %} <!-- this line of code is matching the resource type to its corresponding icon in cards.yml -->

<!--
  <div class="card {% if card.inline == false %}hoverable{% endif %}" style="margin-bottom: 20px;" data-domain="{{ card.domain }}" data-subdomain="{{ card.subdomain }}">
-->
  <div class="card {% if card.inline == false %}hoverable{% endif %}" style="margin-bottom: 20px;" data-domain="{{ card.domain | join: ',' }}" data-subdomain="{{ card.subdomain | join: ',' }}">
    <div class="row no-gutters">
      <div class="team">
        <div class="card-body">
          {% if card.inline == false %}<a href="{{ card.url | relative_url }}">{% endif %}
            <h5 class="card-title"><i class="{{ resource.icon | default: 'fas fa-file' }}"></i>&nbsp;&nbsp; {{ card.title }}</h5></a>
          <p class="card-text"><small class="test-muted">{% if card.profile.date %}<i class="fa-solid fa-calendar"></i>&nbsp; Date: {{ card.profile.date | replace: '<br />', ', ' }}{% endif %}
            {% if card.profile.date and card.profile.author %}&nbsp;&nbsp;//&nbsp;&nbsp;{% endif %}
            {% if card.profile.author %}<i class="fa-solid fa-user"></i>&nbsp; Author: {{ card.profile.author | replace: '<br />', ', ' }}{% endif %}</small></p>
          {% if card.inline == false %}<a href="{{ card.url | relative_url }}">{% endif %}
            <p class="card-text">
              {% assign words = card.teaser | number_of_words %}
              {% if words > 150 %}
              {% assign teaser_words = card.teaser | split: ' ' | slice: 0, 150 | join: ' ' %}
              {{ teaser_words }} <span style="color: #0140A8;">[Read More]</span>
              {% else %}
              {{ card.teaser }}
              {% endif %}
            </p>
          {% if card.keywords.size > 0 %}
            <hr class="solid">
            <p class="card-text test-muted keyword"><small>Keywords: {% for keyword in card.keywords %}<i class="fa-solid fa-hashtag fa-sm"></i>&nbsp;{{ keyword }}&nbsp;&nbsp;{% endfor %}</small></p>
            {% endif %}
            </a>
            {% if card.profile.source or card.profile.license %}
              <hr class="solid">
              {% endif %}
              <p class="card-text">
                {% if card.profile.source %}<small class="test-muted"><i class="fas fa-link"></i> Source: <a href="{{ card.profile.source }}">{{ card.profile.source | replace: '<br />', ', ' }}</a></small>{% endif %}
                {% if card.profile.source and card.profile.license %}<br>{% endif %}
                {% if card.profile.license %}<small class="test-muted"><i class="fa-solid fa-quote-left"></i>&nbsp; License: {{ card.profile.license }}</small>{% endif %}
              </p>
              <hr class="solid">
              <p class="card-text">
                {% assign domain_array = card.domain | split: ',' %}
                {% assign domain_array_size = domain_array_size | size %}
                  <small class="test-muted domain"><i class="fa-solid fa-square"></i> &nbsp; Domain: 
                  {% if domain_array_size > 1 %}
                    {% for d in card.domain %}
                    <a href="{{ site.url }}{{ site.baseurl }}{{ d | downcase | replace: ' ', '-' }}">{{ d }}</a>,&nbsp;   
                      {% endfor %}
                    {% endif %}
                  </small>
                 
                  <!--<small class="test-muted domain"><i class="fa-solid fa-square"></i> &nbsp; Domain: <a href="{{ site.url }}{{ site.baseurl }}{{ card.domain | downcase | replace: ' ', '-' }}">{{ card.domain }}</a> &nbsp;&nbsp;//&nbsp;&nbsp;</small><br>
                  Domain: <a href="{{ site.url }}{{ site.baseurl }}{{ card.domain | downcase | replace: ' ', '-' }}">{{ card.domain }}</a> &nbsp;&nbsp;//&nbsp;&nbsp;</small>-->
                  <small class="test-muted subdomain"><i class="fa-solid fa-sitemap"></i>&nbsp; Subdomain: {{ card.subdomain }} &nbsp;&nbsp;//&nbsp;&nbsp;</small>
                  <small class="test-muted resource"><i class="{{ resource.icon | default: 'fas fa-file' }}"></i>&nbsp; Type of Resource: {{ card.resource }}</small><br>
                
                
              </p>
            </div>
          </div>
        </div>
      </div>
      {% endfor %}
    </div>

<script>
document.addEventListener('DOMContentLoaded', function() {
  const domainFilter = document.getElementById('domain-filter');
  const subdomainFilter = document.getElementById('subdomain-filter');
  const resourceFilter = document.getElementById('resource-filter');
  const keywordLinks = document.querySelectorAll('.keyword-filter');
  const cards = document.querySelectorAll('.card');
  const searchInput = document.getElementById('search-input');
  const clearSearchBtn = document.getElementById('clear-search');
  const searchBtn = document.getElementById('search-button');

  // Define a mapping of subdomains to corresponding domains
  const subdomainToDomain = {
    'All': 'All',
    'Defining Data': 'Understanding Data',
    'Critiquing Data': 'Understanding Data',
    'Acting Ethically with Data': 'Understanding Data',
    'Advocating with Data': 'Understanding Data',
    'Collecting Data': 'Processing Data',
    'Organizing and Cleaning Data': 'Processing Data',
    'Analyzing Data': 'Processing Data',
    'Storing and Preserving Data': 'Processing Data',
    'Making Claims with Data': 'Persuading with Data',
    'Visualizing Data': 'Persuading with Data',
    'Mapping Data': 'Persuading with Data',
    'Telling Stories with Data': 'Persuading with Data'
  };

  function filterCards() {
    const selectedDomain = domainFilter.value;
    const selectedSubdomain = subdomainFilter.value;
    const selectedResource = resourceFilter.value;

    cards.forEach(card => {
      const domains = card.getAttribute('data-domain').split(','); // Get domains from data attribute and split into array
      const subdomains = card.getAttribute('data-subdomain').split(',');; // Get subdomain from data attribute and split into array
      const resource = card.querySelector('.resource').textContent.trim().replace('Type of Resource: ', ''); 

      //const domainMatch = selectedDomain === 'all' || domain === selectedDomain;
      const domainMatch = selectedDomain === 'all' || domains.includes(selectedDomain);
      const subdomainMatch = selectedSubdomain === 'all' || subdomains.includes(selectedSubdomain);
      const resourceMatch = selectedResource === 'all' || resource === selectedResource;

      if (domainMatch && subdomainMatch && resourceMatch) {
        card.style.display = 'block';
      } else {
        card.style.display = 'none';
      }
    });
  }

  domainFilter.addEventListener('change', filterCards);
  subdomainFilter.addEventListener('change', function() {
    // Update the domain filter based on the selected subdomain
    const selectedSubdomain = subdomainFilter.value;
    const correspondingDomain = subdomainToDomain[selectedSubdomain];
    if (correspondingDomain) {
      domainFilter.value = correspondingDomain;
    } else if (selectedSubdomain === 'all') {
      domainFilter.value = 'all'; // Set domain filter to 'all' if 'all' is selected for subdomain
    }
    filterCards();
  });
  
  resourceFilter.addEventListener('change', filterCards);

  keywordLinks.forEach(link => {
    link.addEventListener('click', function(event) {
      event.preventDefault();
      const selectedKeyword = this.getAttribute('data-keyword');
      filterCardsByKeyword(selectedKeyword);
    });
  });

  searchInput.addEventListener('input', function() {
    filterCardsBySearch(this.value.trim());
  });

  searchBtn.addEventListener('click', function() {
    searchInput.form.submit();
  });

  clearSearchBtn.addEventListener('click', function() {
    searchInput.value = '';
    filterCardsBySearch('');
  });

  function filterCardsBySearch(keyword) {
    cards.forEach(card => {
      const cardText = card.textContent.toLowerCase();
      if (cardText.includes(keyword.toLowerCase())) {
        card.style.display = 'block';
      } else {
        card.style.display = 'none';
      }
    });
  }

  // Initial filtering when the page loads
  filterCards();

    // Automatically load the "Understanding Data" selection in the "domains" filter
  //domainFilter.value = 'Understanding Data';
  resourceFilter.value = 'Teaching Module';
  filterCards(); // Trigger filter after changing the selection
});
</script>