

# mozcookiejar
`import "github.com/frioux/mozcookiejar"`

* [Overview](#pkg-overview)
* [Index](#pkg-index)
* [Examples](#pkg-examples)

## <a name="pkg-overview">Overview</a>
mozcookiejar gives access to cookies stored in SQLite by Mozilla products.

The general idea is that you can log into some website via Firefox (or whatever)
and then have your CLI tools effectively be logged in as well.

It is up to you to create a cookie jar and to connect to the SQLite database.
The example below should make it clear how to safely and correctly do that.  I
have tested the github.com/mattn/go-sqlite3 SQLite driver, but I am pretty sure
any of the various SQLite drivers will work.

This library does not support any of the following Cookie fields:


	* MaxAge
	* HttpOnly
	* Raw
	* Unparsed

See documentation for the underlying format here: <a href="http://kb.mozillazine.org/Cookies.sqlite">http://kb.mozillazine.org/Cookies.sqlite</a>



#### <a name="example_">Example</a>

Code:
``` go
jar, err := cookiejar.New(&cookiejar.Options{PublicSuffixList: publicsuffix.List})
if err != nil {
    fmt.Fprintf(os.Stderr, "Failed to build cookies: %s\n", err)
    os.Exit(1)
}
db, err := sql.Open("sqlite3", os.Getenv("MOZ_COOKIEJAR"))
if err != nil {
    fmt.Fprintf(os.Stderr, "Failed to open db: %s\n", err)
    os.Exit(1)
}
defer db.Close()

err = mozcookiejar.LoadIntoJar(db, jar)
if err != nil {
    fmt.Fprintf(os.Stderr, "Failed to load cookies: %s\n", err)
    os.Exit(1)
}
ua := http.Client{Jar: jar}

resp, err := ua.Get("https://some.authenticated.com/website")
if err != nil {
    fmt.Fprintf(os.Stderr, "Failed to fetch page: %s\n", err)
    os.Exit(1)
}
io.Copy(os.Stdout, resp.Body)
```


## <a name="pkg-index">Index</a>
* [func LoadIntoJar(db *sql.DB, jar *cookiejar.Jar) error](#LoadIntoJar)

#### <a name="pkg-examples">Examples</a>
* [Package](#example_)

#### <a name="pkg-files">Package files</a>
[cookiejar.go](https://github.com/frioux/mozcookiejar/tree/master/cookiejar.go) 





## <a name="LoadIntoJar">func</a> [LoadIntoJar](https://github.com/frioux/mozcookiejar/tree/master/cookiejar.go?s=1453:1507#L48)
``` go
func LoadIntoJar(db *sql.DB, jar *cookiejar.Jar) error
```
LoadIntoJar populates the cookie jar from values in database.









## Copyright 2018 Arthur Axel fREW Schmidt

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

     http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
