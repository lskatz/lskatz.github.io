---
layout: splash
---

        <!-- recipe post-content -->
        <div class="post-content">
          <!-- ingredients -->
          <div>
            <h3>Ingredients</h3>
            <ul>{% for ingredient in page.ingredients %}
              <li itemprop="recipeIngredient">{{ ingredient[1] }} {{ ingredient[0] }}</li>{% endfor %}
            </ul>
          </div>
          <!-- ingredients -->

          <blockquote> {{content}} </blockquote>

          <!-- instructions -->
          <div itemprop="recipeInstructions">{% for instruction in page.instructions %}
            <h3 itemprop="HowToSection" id="{{ instruction[0] | slugify }}">{{ instruction[0] }}</h3>
            <ol>{% for step in instruction[1] %}
              <li itemprop="HowToStep">{{ step }}</li>{% endfor %}
            </ol>{% endfor %}
          </div>
          <!-- instructions -->

          <!-- timing -->{% if page.prepmins or page.cookmins %}
          <dl class="dl-horizontal">
            {% if page.prepmins %}<dt>Preparation time</dt><dd>{{ page.prepmins }} min</dd>{% endif %}
            {% if page.cookmins %}<dt>Cooking time</dt><dd>{{ page.cookmins }} mins</dd>{% endif %}
          </dl>
          {% endif %}<!-- timing -->
        </div>
        <!-- recipe post-content -->
