Lighttpd reverse proxy with static files
========================================

Add "mod_proxy" to server.modules

::
    $HTTP["host"] =~ "sse-db.shangshunginstitute.org" {
        server.name     = "sse-db.shangshunginstitute.org"
        server.document-root = "/home/shop/SSP_manager/publishing_manager/"
        $HTTP["url"] !~ "^/(static|media)" {
            proxy.server  = ( "" =>
                (( "host" => "78.47.135.2", "port" => 8008 ))
            )
        }
    }
