# Controllers

Recebe a requisição HTTP, valida o formato de entrada e chama o Service correspondente. Não contém regra de negócio aqui: se um Controller está decidindo algo além de "chamar o Service certo e devolver a resposta certa", essa lógica deveria estar em Services/.
