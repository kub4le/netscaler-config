Configuration of stream selectors to count occurence rate of each url separately. The second condition is most probably not required. It is here just to check whether any other url-s are being counted. TODO: check if a single selector is enough  

`add stream selector selector_url_a "http.REQ.HEADER(\"User-Agent\")" "http.REQ.URL.CONTAINS(\"url_a\")"`  
`add stream selector selector_url_b "http.REQ.HEADER(\"User-Agent\")" "http.REQ.URL.CONTAINS(\"url_b\")"`  
`add stream selector selector_url_c "http.REQ.HEADER(\"User-Agent\")" "http.REQ.URL.CONTAINS(\"url_c\")"`  
`add stream selector selector_url_d "http.REQ.HEADER(\"User-Agent\")" "http.REQ.URL.CONTAINS(\"url_d\")"`  


Configuration of limitidentifiers which call stream selectors. Limit identifier defines actual rate limit of 1 request per timeslice:  

`add ns limitIdentifier limitid_a -timeSlice 30000 -mode REQUEST_RATE -limitType SMOOTH -selectorName selector_url_a`  
`add ns limitIdentifier limitid_b -timeSlice 30000 -mode REQUEST_RATE -limitType SMOOTH -selectorName selector_url_b`  
`add ns limitIdentifier limitid_c -timeSlice 30000 -mode REQUEST_RATE -limitType SMOOTH -selectorName selector_url_c`  
`add ns limitIdentifier limitid_d -timeSlice 30000 -mode REQUEST_RATE -limitType SMOOTH -selectorName selector_url_d`  


Configuration of responder policies to block excessive traffic and respond with apropriate error message. Responder first check the url and if it matches and checks the second condition which is a limitidentifier. If the limitIdentifier returns true (rate exceeded) then responder action is performed.  

`add responder policy resp_pol_a http.REQ.URL.CONTAINS(\"url_a\")" && "SYS.CHECK_LIMIT(\"limitid_a\") && resp_act_respondwith429 -logAction ratelimit_log_action`  
`add responder policy resp_pol_b http.REQ.URL.CONTAINS(\"url_b\")" && "SYS.CHECK_LIMIT(\"limitid_b\") &&resp_act_respondwith429 -logAction ratelimit_log_action`  
`add responder policy resp_pol_c http.REQ.URL.CONTAINS(\"url_c\")" && "SYS.CHECK_LIMIT(\"limitid_c\") && resp_act_respondwith429 -logAction ratelimit_log_action`  
`add responder policy resp_pol_d http.REQ.URL.CONTAINS(\"url_d\")" && "SYS.CHECK_LIMIT(\"limitid_d\") && resp_act_respondwith429 -logAction ratelimit_log_action`  
