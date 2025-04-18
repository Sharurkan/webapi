---


---

<h1 id="bonnes-pratiques-restful--clean-architecture-avec-.net-8">Bonnes Pratiques RESTful &amp; Clean Architecture (avec .NET 8)</h1>
<h2 id="📝-titre-du-document">📝 Titre du document</h2>
<p><strong>Guide de bonnes pratiques pour une Web API en .NET 8</strong><br>
<strong>Architecture : RESTful + SOLID + Clean Architecture</strong></p>
<p><strong>Auteur</strong> : <em>Toi, le boss du backend</em> 🚀<br>
<strong>Date</strong> : Avril 2025</p>
<hr>
<h2 id="✨-objectif">✨ Objectif</h2>
<p>Regrouper les bonnes pratiques pour une API RESTful propre en .NET 8 avec une architecture SOLID/Clean, en particulier dans un contexte avec services, repositories et base de données (ex : DB2/400 via ODBC).</p>
<hr>
<h2 id="🔹-restful-api--bonnes-pratiques">🔹 RESTful API : Bonnes Pratiques</h2>
<h3 id="✍️-design-des-routes">✍️ Design des Routes</h3>

<table>
<thead>
<tr>
<th>Cas</th>
<th>Route RESTful recommandée</th>
<th>Commentaire</th>
</tr>
</thead>
<tbody>
<tr>
<td>Accès par ID primaire</td>
<td><code>GET /api/entities/123</code></td>
<td>Standard REST</td>
</tr>
<tr>
<td>Accès par champ unique</td>
<td><code>GET /api/entities?code=456</code></td>
<td>Meilleur que <code>/by-code/456</code></td>
</tr>
<tr>
<td>Recherches filtrées</td>
<td><code>GET /api/entities?region=FR&amp;label=abc</code></td>
<td>Combinaison flexible de filtres</td>
</tr>
<tr>
<td>Recherches complexes</td>
<td><code>POST /api/entities/search</code></td>
<td>Avec un corps JSON de critères</td>
</tr>
</tbody>
</table><h3 id="🔢-verbes-http--statuts">🔢 Verbes HTTP &amp; Statuts</h3>

<table>
<thead>
<tr>
<th>Action</th>
<th>Verbe</th>
<th>Code HTTP</th>
<th>Exemple de retour</th>
</tr>
</thead>
<tbody>
<tr>
<td>Créer</td>
<td>POST</td>
<td>201 Created</td>
<td><code>CreatedAtRoute(..., entityDto)</code></td>
</tr>
<tr>
<td>Lire (par ID)</td>
<td>GET</td>
<td>200 OK / 404</td>
<td><code>return Ok(dto)</code> ou <code>return NotFound()</code></td>
</tr>
<tr>
<td>Modifier total</td>
<td>PUT</td>
<td>204 / 404</td>
<td><code>return NoContent()</code></td>
</tr>
<tr>
<td>Modifier partiel</td>
<td>PATCH</td>
<td>204 / 404</td>
<td>Idem</td>
</tr>
<tr>
<td>Supprimer</td>
<td>DELETE</td>
<td>204 / 404</td>
<td><code>return NoContent()</code></td>
</tr>
</tbody>
</table><h3 id="🔒-retour-json">🔒 Retour JSON</h3>
<ul>
<li>Ne jamais exposer les entités du domaine directement</li>
<li>Toujours mapper vers des <strong>DTOs</strong></li>
</ul>
<h3 id="✅-validation--erreurs">✅ Validation &amp; Erreurs</h3>
<ul>
<li>Utiliser <code>[ApiController]</code> pour validation auto</li>
<li>Retourner des <code>400 BadRequest</code> explicites</li>
<li>Ne pas utiliser <code>200</code> pour des erreurs logiques</li>
</ul>
<hr>
<h2 id="🔹-clean-architecture--solid">🔹 Clean Architecture / SOLID</h2>
<h3 id="⚙️-couches--responsabilités">⚙️ Couches &amp; Responsabilités</h3>

<table>
<thead>
<tr>
<th>Couche</th>
<th>Rôle principal</th>
<th>Retour recommandé</th>
</tr>
</thead>
<tbody>
<tr>
<td>Controller</td>
<td>Orchestration + HTTP</td>
<td><code>IActionResult</code></td>
</tr>
<tr>
<td>Service</td>
<td>Logique métier (validation, règles, contrôles)</td>
<td><code>Task&lt;bool&gt;</code>, <code>Task&lt;Entity?&gt;</code>, <code>Result&lt;T&gt;</code></td>
</tr>
<tr>
<td>Repository</td>
<td>Accès brut aux données (ODBC, SQL, ORM…)</td>
<td><code>Task&lt;Entity?&gt;</code>, <code>Task&lt;int&gt;</code></td>
</tr>
<tr>
<td>DTOs</td>
<td>Entrée/Sortie API uniquement</td>
<td>N/A</td>
</tr>
<tr>
<td>Entities</td>
<td>Représentation du domaine</td>
<td>Utilisée en interne</td>
</tr>
</tbody>
</table><h3 id="🔄-mappage">🔄 Mappage</h3>
<ul>
<li>Utiliser AutoMapper ou un mapper manuel dans une couche à part</li>
</ul>
<h3 id="🎓-retour-des-méthodes-bonne-pratique">🎓 Retour des méthodes (bonne pratique)</h3>

<table>
<thead>
<tr>
<th>Action</th>
<th>Controller</th>
<th>Service</th>
<th>Repository</th>
</tr>
</thead>
<tbody>
<tr>
<td>GET</td>
<td><code>Ok(dto)</code> / <code>404</code></td>
<td><code>Entity?</code> / <code>Result&lt;T&gt;</code></td>
<td><code>Entity?</code></td>
</tr>
<tr>
<td>POST</td>
<td><code>CreatedAtRoute()</code></td>
<td><code>int</code> / <code>Result&lt;T&gt;</code></td>
<td><code>int</code> / <code>Guid</code></td>
</tr>
<tr>
<td>PUT</td>
<td><code>204</code> / <code>404</code></td>
<td><code>bool</code> / <code>Result</code></td>
<td><code>bool</code></td>
</tr>
<tr>
<td>PATCH</td>
<td><code>204</code> / <code>404</code></td>
<td><code>bool</code> / <code>Result</code></td>
<td><code>bool</code></td>
</tr>
<tr>
<td>DELETE</td>
<td><code>204</code> / <code>404</code></td>
<td><code>bool</code> / <code>Result</code></td>
<td><code>bool</code></td>
</tr>
</tbody>
</table><h3 id="✅-classe-générique-resultt">✅ Classe générique <code>Result&lt;T&gt;</code></h3>
<pre class=" language-csharp"><code class="prism  language-csharp"><span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">Result</span><span class="token operator">&lt;</span>T<span class="token operator">&gt;</span>
<span class="token punctuation">{</span>
    <span class="token keyword">public</span> <span class="token keyword">bool</span> IsSuccess <span class="token punctuation">{</span> <span class="token keyword">get</span><span class="token punctuation">;</span> <span class="token keyword">private</span> <span class="token keyword">set</span><span class="token punctuation">;</span> <span class="token punctuation">}</span>
    <span class="token keyword">public</span> <span class="token keyword">string</span><span class="token operator">?</span> ErrorMessage <span class="token punctuation">{</span> <span class="token keyword">get</span><span class="token punctuation">;</span> <span class="token keyword">private</span> <span class="token keyword">set</span><span class="token punctuation">;</span> <span class="token punctuation">}</span>
    <span class="token keyword">public</span> T<span class="token operator">?</span> Data <span class="token punctuation">{</span> <span class="token keyword">get</span><span class="token punctuation">;</span> <span class="token keyword">private</span> <span class="token keyword">set</span><span class="token punctuation">;</span> <span class="token punctuation">}</span>

    <span class="token keyword">public</span> <span class="token keyword">static</span> Result<span class="token operator">&lt;</span>T<span class="token operator">&gt;</span> <span class="token function">Success</span><span class="token punctuation">(</span>T data<span class="token punctuation">)</span> <span class="token operator">=</span><span class="token operator">&gt;</span> <span class="token keyword">new</span> <span class="token class-name">Result</span><span class="token operator">&lt;</span>T<span class="token operator">&gt;</span> <span class="token punctuation">{</span> IsSuccess <span class="token operator">=</span> <span class="token keyword">true</span><span class="token punctuation">,</span> Data <span class="token operator">=</span> data <span class="token punctuation">}</span><span class="token punctuation">;</span>
    <span class="token keyword">public</span> <span class="token keyword">static</span> Result<span class="token operator">&lt;</span>T<span class="token operator">&gt;</span> <span class="token function">Failure</span><span class="token punctuation">(</span><span class="token keyword">string</span> error<span class="token punctuation">)</span> <span class="token operator">=</span><span class="token operator">&gt;</span> <span class="token keyword">new</span> <span class="token class-name">Result</span><span class="token operator">&lt;</span>T<span class="token operator">&gt;</span> <span class="token punctuation">{</span> IsSuccess <span class="token operator">=</span> <span class="token keyword">false</span><span class="token punctuation">,</span> ErrorMessage <span class="token operator">=</span> error <span class="token punctuation">}</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span>
</code></pre>
<p><strong>Utilisation dans un service</strong></p>
<pre class=" language-csharp"><code class="prism  language-csharp"><span class="token keyword">public</span> <span class="token keyword">async</span> Task<span class="token operator">&lt;</span>Result<span class="token operator">&lt;</span>UserDto<span class="token operator">&gt;</span><span class="token operator">&gt;</span> <span class="token function">GetByEmailAsync</span><span class="token punctuation">(</span><span class="token keyword">string</span> email<span class="token punctuation">)</span>
<span class="token punctuation">{</span>
    <span class="token keyword">var</span> user <span class="token operator">=</span> <span class="token keyword">await</span> _repository<span class="token punctuation">.</span><span class="token function">GetByEmailAsync</span><span class="token punctuation">(</span>email<span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token keyword">if</span> <span class="token punctuation">(</span>user <span class="token operator">==</span> <span class="token keyword">null</span><span class="token punctuation">)</span>
        <span class="token keyword">return</span> Result<span class="token operator">&lt;</span>UserDto<span class="token operator">&gt;</span><span class="token punctuation">.</span><span class="token function">Failure</span><span class="token punctuation">(</span><span class="token string">"User not found"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

    <span class="token keyword">return</span> Result<span class="token operator">&lt;</span>UserDto<span class="token operator">&gt;</span><span class="token punctuation">.</span><span class="token function">Success</span><span class="token punctuation">(</span>_mapper<span class="token punctuation">.</span><span class="token generic-method function">Map<span class="token punctuation">&lt;</span>UserDto<span class="token punctuation">&gt;</span></span><span class="token punctuation">(</span>user<span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span>
</code></pre>
<p><strong>Dans un contrôleur (RESTful)</strong></p>
<pre class=" language-csharp"><code class="prism  language-csharp"><span class="token punctuation">[</span>HttpGet<span class="token punctuation">]</span>
<span class="token keyword">public</span> <span class="token keyword">async</span> Task<span class="token operator">&lt;</span>IActionResult<span class="token operator">&gt;</span> <span class="token function">GetByEmail</span><span class="token punctuation">(</span><span class="token punctuation">[</span>FromQuery<span class="token punctuation">]</span> <span class="token keyword">string</span> email<span class="token punctuation">)</span>
<span class="token punctuation">{</span>
    <span class="token keyword">var</span> result <span class="token operator">=</span> <span class="token keyword">await</span> _userService<span class="token punctuation">.</span><span class="token function">GetByEmailAsync</span><span class="token punctuation">(</span>email<span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token keyword">if</span> <span class="token punctuation">(</span><span class="token operator">!</span>result<span class="token punctuation">.</span>IsSuccess<span class="token punctuation">)</span>
        <span class="token keyword">return</span> <span class="token function">NotFound</span><span class="token punctuation">(</span><span class="token keyword">new</span> <span class="token punctuation">{</span> error <span class="token operator">=</span> result<span class="token punctuation">.</span>ErrorMessage <span class="token punctuation">}</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

    <span class="token keyword">return</span> <span class="token function">Ok</span><span class="token punctuation">(</span>result<span class="token punctuation">.</span>Data<span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span>
</code></pre>
<hr>
<p>📄 <strong>Pied de page</strong> :</p>
<blockquote>
<p>Document de référence pour projets RESTful en .NET 8 — Clean, testable &amp; scalable.<br>
Partage librement dans ta team backend 🧠🔥</p>
</blockquote>

