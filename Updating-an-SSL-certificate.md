1. Check the certificate files into git in the `/sites/ycharts/confs/ssl` directory.

1. Upload the certificate to IAM
    ```bash
    aws iam upload-server-certificate \
        --server-certificate-name geotrust-01-2016 \
        --certificate-body file:///sites/ycharts/confs/ssl/ycharts.com.crt \
        --private-key file:///sites/ycharts/confs/ssl/ycharts.com.key \
        --certificate-chain file:///sites/ycharts/confs/ssl/geotrust_intermediate_ca.crt \
        --path /cloudfront/
    ```

1. Update SSL certificate for:
    - Staging node load balancer
    - Staging web load balancer
    - Production node load balancer
    - Production web load balancer
    - media.ycharts.com Cloudfront Distribution
    - media-dev.ycharts.com Cloudfront Distribution
    - static.ycharts.com Cloudfront Distribution
    - static-dev.ycharts.com Cloudfront Distribution
