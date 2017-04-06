# SpiderMonkey
Componentes: Rute da Silva Barbalho (20141011110123), Clara Alice Bandeira de Moura (20141011110212) e Tuane Salviano Peres (20141011110026).<br>
Nicknames: rutebarbalho; claraabandeira; tuaneperes

#### Resumo ####
  SpiderMonkey é utilizado como um motor do Mozilla, ele compila e executa scripts que contêm instruções e funções de JavaScript, lidando com a alocação de memória para os objetos necessários para executar scripts, e ele tabém limpa o lixo e recolhe os objetos que já não são necessários. Criada em 1995 pela Netscape e mantida atualmente pala Mozilla Foundation.
#### Instalação e Uso ####

  <p> Para utilização do SpiderMonkey, caso o projeto seja em C++ ou C, será necessario fazer o download do plugin no Firefox (na aba "Aplicativos") e a inclusão do JSAPI, utilizando o cabeçalho `jsapi.h`. Porém alguns sistemas como Debian fornecem o SpiderMonkey como pacote pré construído.</p>
   <p> Para executar qualquer código JavaScript em SpiderMonkey, um projeto deve ter três elementos-chave : a JSRuntime , um JSContext e um objeto global.</p>
    <p></b>JSRuntime:</b> é o espaço no qual as variáveis de JavaScript, objetos, scripts e contextos (que serão explicados mais a frente) usados pelo seu aplicativo são alocados. Normalmente, os aplicativos só precisam de um tempo de execução (runtime).</p>
   <p> </b>JSContext:</b> É o compilador JavaScript. Este maquinário interpreta as funções, scripts, objetos e dados referidos no código. Pode-se conter apenas um contexto por aplicativo, mas o desenvolvedor deve estar ciente que o compilador só executa uma função de cada vez, o que torna a compilação lenta. Por isso aconcelha-se o uso de vários contextos para cada área do código.</p>
   <p> Segue exemplo da codificação para um simples "Hello Word", para fins de melhor entendimento e explicação de funções:</p>
  
 ```    
// following code might be needed in some case
// #define __STDC_LIMIT_MACROS
// #include <stdint.h>
#include "jsapi.h"
/* Cria um novo objeto global, contêm classes */
static JSClass global_class = {
    "global",
    JSCLASS_GLOBAL_FLAGS,
    JS_PropertyStub,
    JS_DeletePropertyStub,
    JS_PropertyStub,
    JS_StrictPropertyStub,
    JS_EnumerateStub,
    JS_ResolveStub,
    JS_ConvertStub,
};

int main(int argc, const char *argv[]) /* Inicia o JS */
{
/* Cria um novo Runtime */
    JSRuntime *rt = JS_NewRuntime(8L * 1024 * 1024, JS_USE_HELPER_THREADS);
    if (!rt)
        return 1;
/* Cria um novo contexto */
    JSContext *cx = JS_NewContext(rt, 8192);
    if (!cx)
        return 1;

    { 
      JSAutoRequest ar(cx); 

      JS::RootedObject global(cx, JS_NewGlobalObject(cx, &global_class, nullptr));
      if (!global)
          return 1;

      JS::RootedValue rval(cx);

      { 
        JSAutoCompartment ac(cx, global);
        JS_InitStandardClasses(cx, global);

        const char *script = "'hello'+'world, it is '+new Date()";
        const char *filename = "noname";
        int lineno = 1;
        bool ok = JS_EvaluateScript(cx, global, script, strlen(script), filename, lineno, rval.address());
        if (!ok)
          return 1;
      }

      JSString *str = rval.toString();
      printf("%s\n", JS_EncodeString(cx, str));
    }

    JS_DestroyContext(cx); //"Destroi" o Context, no caso, limpa o Contexto
    JS_DestroyRuntime(rt); //"Destroi" o Runtime, no caso, limpa o Runtime
    JS_ShutDown(); // "Desliga" o JS
    return 0;
}
```
    
