# Migration from Traefik v2.2 to v3.5.4

This role has been updated from Traefik v2.2 to v3.5.4. Here are the key changes and migration considerations:

## Changes Made

### Docker Image Update
- **Before**: `traefik:v2.2`
- **After**: `traefik:v3.5.4`

## Important v3 Migration Notes

### Breaking Changes from v2 to v3

According to the [official Traefik v2 to v3 migration guide](https://doc.traefik.io/traefik/migrate/v2-to-v3/), the following changes may affect your configuration:

#### 1. Router Rule Syntax (Dynamic Configuration)
Traefik v3 introduces a new rule matcher syntax. For backward compatibility during migration:
- The role will work with existing v2 syntax configurations
- To enable v2 compatibility mode explicitly, add to your `traefik.config`:
  ```yaml
  core:
    defaultRuleSyntax: v2
  ```

#### 2. Deprecated Options Removed
The following options have been removed in v3:
- `sslRedirect`, `sslTemporaryRedirect`, `sslHost`, `sslForceHost`, `featurePolicy` in Headers middleware
- `forceSlash` in StripPrefix middleware
- `preferServerCipherSuites` option

#### 3. HTTP/3 Support
HTTP/3 is no longer experimental and can be configured directly on entrypoints without the `experimental.http3` option.

#### 4. IPWhiteList Renamed
- The `IPWhiteList` middleware has been renamed to `IPAllowList` (no configuration changes needed)

### Recommended Migration Steps

1. **Test in Development First**
   - Deploy to a test environment first
   - Verify all routing rules work correctly
   - Check that SSL certificates are properly managed

2. **Review Your Configuration**
   - Check if you're using any deprecated options listed above
   - Update any middleware names (IPWhiteList → IPAllowList)
   - Verify your router rules are compatible

3. **Enable v2 Compatibility (Optional)**
   - If you have complex routing rules, enable v2 syntax compatibility temporarily:
     ```yaml
     traefik:
       config:
         core:
           defaultRuleSyntax: v2
         # ... rest of your configuration
     ```

4. **Monitor After Deployment**
   - Watch logs for deprecation warnings
   - Verify SSL certificate renewal still works
   - Check all routes are accessible

### New Features in v3

- Improved performance and stability
- Native HTTP/3 support
- Better Kubernetes Gateway API support
- Enhanced observability with OpenTelemetry
- Improved middleware capabilities

### Resources

- [Traefik v3 Documentation](https://doc.traefik.io/traefik/)
- [v2 to v3 Migration Guide](https://doc.traefik.io/traefik/migrate/v2-to-v3/)
- [Configuration Details for v3](https://doc.traefik.io/traefik/migrate/v2-to-v3-details/)

## Example Updated Configuration

Here's an example configuration updated for v3 best practices:

```yaml
traefik:
  docker_container:
    networks:
      - name: bridge
      - name: web
    env:
      CF_API_EMAIL: "{{ cloudflare_email }}"
      CF_API_KEY: "{{ cloudflare_api_key }}"
  config:
    log:
      level: "INFO"
    
    entryPoints:
      http:
        address: ":80"
      https:
        address: ":443"
        http3: {}  # Enable HTTP/3 directly on entrypoint
    
    api:
      dashboard: true
    
    providers:
      docker:
        endpoint: "unix:///var/run/docker.sock"
        watch: true
        exposedByDefault: false
      file:
        filename: "/etc/traefik/traefik.yaml"
    
    certificatesResolvers:
      letsencrypt:
        acme:
          email: "admin@example.com"
          storage: "/acme.json"
          httpChallenge:
            entryPoint: http
          # or use DNS challenge
          # dnsChallenge:
          #   provider: cloudflare
          #   delayBeforeCheck: 0
    
    http:
      middlewares:
        redirect-to-https:
          redirectScheme:
            scheme: "https"
        
        dashboard-auth:
          basicAuth:
            users:
              - "admin:$apr1$..."
        
        # Note: IPWhiteList is now IPAllowList (but still works with old name)
        local-only:
          ipAllowList:
            sourceRange:
              - "127.0.0.1/32"
              - "10.0.0.0/8"
      
      routers:
        dashboard-redirect:
          entryPoints:
            - http
          rule: "Host(`traefik.example.com`)"
          service: noop@internal
          middlewares:
            - redirect-to-https
        
        dashboard:
          entryPoints:
            - https
          rule: "Host(`traefik.example.com`)"
          service: api@internal
          middlewares:
            - dashboard-auth
          tls:
            certResolver: letsencrypt
```

## Rollback Plan

If you encounter issues after upgrading:

1. **Immediate Rollback**: Change the image back to `traefik:v2.2` in your playbook
2. **Redeploy**: Run the ansible playbook again
3. **Investigate**: Review logs and configuration differences

## Support

For issues specific to this Ansible role, please open an issue on the role repository.

For Traefik-specific questions, refer to:
- [Traefik Community Forum](https://community.traefik.io/)
- [Traefik Documentation](https://doc.traefik.io/traefik/)
