#Small Code-Samples

## Upload a picture using forms

```csharp
var request = new HTTPRequest(new Uri("http://server.com"), HTTPMethods.Post, onFinished);
request.AddBinaryData("image", texture.EncodeToPNG(), "image.png", "image/png");
request.Send();
```

## Upload a picture without forms, sending only the raw data

```csharp
var request = new HTTPRequest(new Uri("http://server.com"), HTTPMethods.Post, onFinished);
request.SetHeader("Content-Type", "image/png");
request.Raw = texture.EncodeToPNG();
request.Send();
```

## Add custom header / send json data

```csharp
string json = "{ 'field': 'value' }";

var request = new HTTPRequest(new Uri("http://server.com"), HTTPMethods.Post, onFinished);
request.SetHeader("Content-Type", "application/json; charset=UTF-8");
request.RawData = UTF8.Encoding.GetBytes(json);
request.Send();
```

## Display download progress

```csharp
var request = new HTTPRequest(new Uri("http://serveroflargefile.net/path"), (req, resp) => {
  Debug.Log("Finished!");
});

request.OnDownloadProgress += (req, down, length) => Debug.Log(string.Format("Progress: {0:P2}", down / (float)length));
request.Send();
```

## Abort a request

```csharp
var request = new HTTPRequest(new Uri(address), (req, resp) => {
	// State should be HTTPRequestStates.Aborted if we call Abort() before
	// it’s finishes
	Debug.Log(req.State);
});

request.Send();

// and then call Abort when the request isn't relevant anymore
request.Abort();
```

## Verify hostnames in HTTPS

```csharp
public sealed class HostNameVerifier : Org.BouncyCastle.Crypto.Tls.ICertificateVerifyer
{
    public bool IsValid(Uri targetUri, SecureProtocol.Org.BouncyCastle.Asn1.X509.X509CertificateStructure[] certs)
    {
        foreach (var cert in certs)
        {
            var values = cert.Subject.GetValueList();
            if (values.Contains(targetUri.Host))
                return true;
        }
 
        return false;
    }
}

HTTPManager.DefaultCertificateVerifyer = new HostNameVerifier();
```