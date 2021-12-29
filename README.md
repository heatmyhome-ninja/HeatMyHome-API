# API for Domestic Energy Performance Certificates (EPC)
URL: http://heatmyhomeninja-env.eba-w2gamium.us-east-2.elasticbeanstalk.com/

Error: {"status":404,"error":"must provide url parameter (either ?postcode=AB123XY or ?certificate=1234-5678-1234-5678)"}

# Request addresses & EPC certificates for a given postcode: 
URL: http://heatmyhomeninja-env.eba-w2gamium.us-east-2.elasticbeanstalk.com/?postcode=XJ120KH

Valid Response: {"status":200,"result":[[address, certificate_number], [address, certificate_number], [...]]}

Error: {"status":404,"error":"postcode <12asdf3> is not valid"}

# Request EPC data for a given EPC certificate: 
URL: http://heatmyhomeninja-env.eba-w2gamium.us-east-2.elasticbeanstalk.com/?certificate=0123-4567-8910-2345-6789

Valid Response: {"status":200,"result":{"floor-area":"109 square metres","space-heating":"5963 kWh per year","valid-until":"19 September 2021"}}

Error: {"status":404,"error":"certificate <1245> is not valid"}
 
  
