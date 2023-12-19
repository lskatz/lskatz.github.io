---
layout: splash
---

        <!-- recipe post-content -->
        <div class="post-content">
          <!-- ingredients -->
          <div>
            <h3>Ingredients</h3>
            <table>
              <tr>
                <th>Ingredient</th><th>How much</th>
              </tr>
              
            {% for ingredient in page.ingredients %}
              <tr>
              <td itemprop="recipeIngredient">{{ ingredient[0] }}</td>
              <td><span style='border:1px dotted grey; background:#EEFFFF;color: #001111;'>{{ ingredient[1] }}</span> </td>
              </tr>
            {% endfor %}
            </table>
          </div>
          <!-- ingredients -->

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

          <h2>Narrative</h2>

          <!-- Headers in the narrative should be at <h3> -->
          <blockquote> {{content}} </blockquote>

          {% endif %}<!-- timing -->
        </div>
        <!-- recipe post-content -->
