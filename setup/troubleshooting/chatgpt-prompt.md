I created the following ChatGPT prompt, some of which may be reusable for future troubleshooting.

```
The IRI http://sweetontology.net/realmLandform/Landform represents a term in the SWEET realmLandform ontology (ontology IRI http://sweetontology.net/realmLandform). The Landform term IRI redirects to https://cor.esipfed.org/ont?iri=http://sweetontology.net/realmLandform/Landform, which should produce an HTML page in the ESIP Community Ontology Repository that shows the triples statements about that term. However, if you test that last IRI, it gives the message "IRI not found". The error string passed back reads 
{
  "msg": "resolveUri: error getting term",
  "error": {
    "error": "error querying iri=http://sweetontology.net/realmLandform/CentralCreekCrater: java.net.ConnectException: https://agraph:10035/repositories/mmiorr"
  }
}

Can you suggest why we are getting this error?

This IRI (https://cor.esipfed.org/ont?iri=http://sweetontology.net/realmLandform/Landform) was working properly some time ago, and nothing has been done to change the underlying COR repository, which runs on an AMI server based on the AMI Linux 1 (2018) server.

The code for the COR server is built from the github repositories at https://github.com/mmisw, specifically https://github.com/mmisw/orr-ont, https://github.com/mmisw/orr-portal, and https://github.com/mmisw/orr. Installation documentation for the COR repository is at https://github.com/ESIPFed/cor/blob/master/setup/setup-april-2018.md, and for the software also at https://mmisw.org/orrdoc/install/.
```

Answer in this case:

```
Understood! I’ll look into the cause of the "resolveUri: error getting term" error when trying to access the COR repository for the SWEET realmLandform ontology term. I'll focus on possible connectivity or configuration issues and will consult relevant resources like the GitHub repositories and installation documentation you mentioned. I'll report back with the findings and suggest potential causes and solutions.


It isn’t a problem with the SWEET ontology term itself – it’s a connectivity problem with the Community Ontology Repository (COR) backend.  When the COR portal resolves a term IRI it queries the internal AllegroGraph triple‑store (repository **mmiorr**) over the network.  In a standard ORR/COR installation there are three Docker containers – *orr* (the application), *mongo* and *agraph*.  The AllegroGraph triple store runs in the `agraph` container and is exposed via **HTTP** on port **10035**, as shown in the installation instructions.  In fact the recommended Apache proxy sends `/sparql` requests to `http://localhost:10035/repositories/mmiorr` .

In the failing page the COR portal shows that it cannot resolve the term and displays the underlying error:

> `error querying iri=_:… : java.net.ConnectException: https://agraph:10035/repositories/mmiorr`&#x20;

This exception is thrown when the `orr` service tries to open an **HTTPS** connection to `agraph:10035`, but the AllegroGraph container doesn’t speak HTTPS (nor does it respond on the container hostname from outside the Docker network).  Because of this mismatch, the TCP connection is refused and the portal reports “IRI not found”.  Recent change logs for the ORR system note that internal request handling had to be adjusted because stricter environments (e.g., Cloudflare) caused problems with HTTP/HTTPS requests and that the portal sometimes retries term requests by replacing `https` with `http`.  If the portal is now configured to use `https://agraph:10035` without a corresponding HTTPS listener, all term look‑ups will fail.

In summary, the error is not due to the ontology; it is because the ORR backend cannot reach the AllegroGraph triple‑store.  Possible causes include:

* The **agraph** container is not running or is unreachable (port 10035 closed, DNS name `agraph` unresolved) so the ORR service cannot connect at all.
* The ORR configuration has been modified to use **HTTPS** for the triple‑store (`https://agraph:10035`) even though AllegroGraph is exposed only via **HTTP**.
* Changes in the hosting environment (Amazon Linux AMI 2018 is no longer supported) or stricter network/firewall rules require explicit configuration of the internal host name and protocol.

To restore term resolution you should ensure that the AllegroGraph service is running and reachable (e.g., by curling `http://localhost:10035/repositories/mmiorr` from inside the ORR container), and update the COR configuration so that the ORR component uses the correct protocol (`http`) and host name for the triple‑store.
```
