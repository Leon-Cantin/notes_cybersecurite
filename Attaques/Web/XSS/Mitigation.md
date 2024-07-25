utiliser librairie come DOMPurify qui échape avec \ tout charactère spécial
Ne pas permettre d'entrées utilisateur dans:
* JavaScript code <script></script>
* CSS Style Code <style></style>
* Tag/Attribute Fields <div name='INPUT'></div>
* HTML Comments <!-- -->  

Éviter les fonctions JS qui permettent de modifier du HTML
* DOM.innerHTML
* DOM.outerHTML
* document.write()
* document.writeln()
* document.domain  

Et ces fonctions JQuery
* html()
 * parseHTML()
* add()
* append()
* prepend()
* after()
* insertAfter()
* before()
* insertBefore()
* replaceAll()
* replaceWith()

Pour un serveur PHP on peut encoder en HTML (< devient &lt). On peut utiliser htmlspecialchars ou htmlentities

Cookie flag: HttpOnly pour éviter lecture en JS et Secure pour pemettre seulement HTTPS
   
