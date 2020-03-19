# react-rules
As regras não escritas do React

A tela só se atualiza com `setState`.

Nunca faça uma verificação que seta o estado fora do `useEffect`.

Quando o estado é um objeto ou array prefira um `setState` síncrono: `setState(prevValue => ...)`

`Component.propTypes` vem acompanhado do `Component.defaultProps` e ambos devem ser usados.

Lógica no meio do código fica feio.