
  # OPENSSL 101 📝  
  Esse projeto tem a inteção de agrupar informações sobre o openssl. 
  
  ## O que é SSL/TLS 🚀  
  Ambos os termos se referem ao mesmo principio: "Proteger os dados que estão sendo trafegados entre sua origem e destino dentro da internet". O termo SSL apareceu em 1994 com o NetScape e o termo TLS apareceu em 1999 com o IETF. Existem várias versões de cada um, são mantidados por organizações diferentes, mas tanto o SSL quanto o TLS são usados como sinônimos.
  
  ## Como o SSL/TLS protegem os dados 🔥  
  O SSL/TLS se propõe a trazer confiabilidade, integridade e autenticiade na transação de dados pela internet. Cada um desses três pontos é aplicado por meio de ferramentas criptográficas diferentes. Segue uma tabela para resumo:
  
  1. Confiabilidade (Encryption)
  
      O dado é acessado apenas pelo servidor e pelo cliente. 
      
  2. Integridade (Hashing) 
  
      O dado é não é alterado durante a transação.   
       
  3. Autenticidade (Public Key Infrasctructure)

      Tanto o servidor quanto o cliente são quem dizem ser.
      
  É importante ressaltar que o SSL/TLS não impede do dado ser interceptado ou alterado, mas permite saber se algumas dessas situações ocorreram. 

  ## Ecossistema SSL/TLS ✨  
  Três entidades compoem um ecosistema SSL/TLS. Um cliente, um servidor e uma autoridade certificadora.
  Um cliente pode ser resumido em qualquer coisa que se conecte a internet e um servidor é um web server por exemplo. 
  
  O cliente é responsável por iniciar o processo de "handshake" com o servidor e o servidor tem o papel de finalizar o handshake iniciado pelo cliente.

  O servidor sempre é autenticado e o cliente pode ser ou não. O que significa que quando um cliente se comunicar com o servidor usando SSL/TLS, o servidor apresentará seu certificado provando sua identidade. Quando o servidor e o cliente precisam provar sua identidade numa transação de dados, temos uma Autenticação Mútua entre as partes.

  Como visto, a comunicação entre o cliente e o servidor é baseada em certificados. A terceira entitdade desse Ecossistema é conhecida como Autoridade Certificadora. É ela a responsável por gerar os certificados que o servidor e o cliente irão confiar. A autoridade certificadora fornece uma "âncora de confiança" entre as partes. Essa âncora é confiável para o cliente e tambem é usada para gerar a identidade do servidor. Dessa forma o cliente pode confiar no servidor. 

  ## Utilização do pacote openssl

  ### Geração de chaves públicas e privadas

  #### Algoritmo RSA

  ``` shell  
  openssl genrsa -out KEY1.pem 2048
  ``` 

  ``` shell  
  openssl genrsa -out KEY2.pem -aes128 4096
  ``` 

  Os comandos acimas geram um chave pública e uma chave privada com o algoritmo RSA e exporta o resultado em um arquivo do tipo .pem. O primeiro comando gera as chaves com o tamanho da chave é de 2048 bits. O segundo comando gera as chaves com o tamanho de 4096 bits e criptografa o arquivo gerado. Existem varias opções de cripografia com openssl. O comando abaixo pode ser utilizado para exibir essas opções.

  ``` shell  
  openssl list -cipher-algorithms
  ```   
  
  Para consultar as chaves geradas use o comando abaixo:

  ``` shell  
  openssl rsa -in KEY.pem -noout -text
  ``` 
  O parametro -noout apresenta as chaves sem o formato PEM do arquivo.

  #### Algoritmo DSA

  ``` shell 
  openssl dsaparam -out DSA-PARAM.pem 1024
  ```
  O comando acima é utilizado para gerar os parametros para criar a chave DSA.
  ``` shell 
  openssl gendsa -out DSA-KEY.pem DSA-PARAM.pem
  ```
  O comando acima é gera a chave DSA com os parametros criados pelo primeiro comando.

  ``` shell  
  openssl dsaparam -genkey -out DSA-PARAM-KEY.pem 2048
  ```
  O comando acima gera os parametros e a chave DSA em um único arquivo.
  
  Para consultar as chaves e os parametros gerados use o comando abaixo:

  ``` shell  
  openssl dsaparam -in DSA-PARAM.pem -text -noout
  ``` 

  ``` shell  
  openssl dsa -in DSA-KEY.pem -text -noout
  ``` 

  O primeiro comando consulta apenas os parametros de um arquivo. E o segundo consulta os parametros e a chave.

  #### Generating Curvas Elípticas

  Comando usado para gerar os parametros para criar chaves do tipo curvas elípticas:
  ``` shell  
  openssl genpkey -genparam -algorithm EC -pkeyopt ec_paramgen_curve:secp384r1 -out EC-PARAM.pem
  ```

  Comando usado para gerar as chaves com os parametros criados anteriormente:
  ``` shell
  openssl genpkey -paramfile EC-PARAM.pem -out EC-KEY.pem
  ```

  O comando abaixo pode ser usado para gerar as chaves sem a necessidade dos parametros:
  ``` shell
  openssl genpkey -algorithm EC -pkeyopt ec_paramgen_curve:P-384 -out EC-KEY.pem
  ```

  Para consultar os parametros e as chaves use os comandos abaixo:

  ``` shell
  openssl ecparam -in EC-PARAM.pem -text -noout
  ```

  ``` shell
  openssl ec -in EC-KEY.pem -text -noout
  ```

  O comando abaixo exibe um lista de opções para tipos diferentes de curvas elípticas:
  ``` shell
  openssl ecparam -list_curves
  ```

  ### Geração de Solicitação de Assinatura de Certificado (CSRs) e Certificado

  #### Gerando Solicitação de Assinatura de Certificado

  O comando abaixo gera um CSR com uma private key existente:
  ``` shell
  openssl req -new -key KEY.pem -out CSR.pem
  ```

  #### Gerando Certificado auto assinado

  O comando abaixo gera um certificado auto assinado com uma private key existente:
  ``` shell
  openssl req -x509 -key KEY.pem -out CERT.pem
  ```

  Para não precisar responder as perguntas pelo promt de comando pode-se usar os argumentos:
  ``` shell
  openssl req -new -key KEY.pem -out CSR.pem -subj "/C=US/CN=site.com" 
  ```

  É possível gerar a solicitação de assinatura de certificado ou o certificado junto da private key com um unico comando.
  ``` shell
  openssl req -x509 -out CERT.pem -nodes -subj "//CN=site.com" -newkey rsa:1024
  ```
  