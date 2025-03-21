# MySQL S3 Import Example

Configuration in this directory creates set of RDS resources including DB instance, DB subnet group and DB parameter group where the database itself is imported from a MySQL Percona Xtrabackup stored in S3.

## Usage

To run this example you need to execute:

```bash
$ terraform init
$ terraform plan
$ terraform apply
```

## Testing

In order to test this example, you will need a database backup in order to upload to S3 and import into the module. A backup has been provided under `backup/`, but in the case that a new backup needs to be created, the steps outlined below should suffice for creating a backup that can be used for the sake of testing and verifying module functionality/changes.

1. Create database container

```bash
$ docker run -d --name percona-server-mysql-5.7.12 -e MYSQL_ROOT_PASSWORD=root percona/percona-server:5.7.12
$ docker exec -it percona-server-mysql-5.7.12 bash
$ mysql -u root -p # password is also root
```

2. Once logged into container and database, create database and user used by RDS

```sql
CREATE DATABASE s3Import;
CREATE USER 's3_import_user'@'localhost' IDENTIFIED BY 'YourPwdShouldBeLongAndSecure!';
GRANT ALL PRIVILEGES ON * . * TO 's3_import_user'@'localhost';
FLUSH PRIVILEGES;
```

3. Use Percona Xtrabackup container to dump database and upload to S3

```bash
$ mkdir -p /tmp/backup
$ docker run --name percona-xtrabackup-2.4 --mount type=bind,src=/tmp/backup,dst=/backup --volumes-from percona-server-mysql-5.7.12 percona/percona-xtrabackup:2.4 xtrabackup --backup --data-dir=/var/lib/mysql --target-dir=/backup --user=root --password=root
$ mv /tmp/backup ./backup
```

Note that this example may create resources which cost money. Run `terraform destroy` when you don't need these resources.

<!-- BEGIN_TF_DOCS -->
## Requirements

| Name | Version |
|------|---------|
| <a name="requirement_terraform"></a> [terraform](#requirement\_terraform) | >= 1.0 |
| <a name="requirement_aws"></a> [aws](#requirement\_aws) | >= 5.89 |

## Providers

| Name | Version |
|------|---------|
| <a name="provider_aws"></a> [aws](#provider\_aws) | >= 5.89 |

## Modules

| Name | Source | Version |
|------|--------|---------|
| <a name="module_aurora"></a> [aurora](#module\_aurora) | ../../ | n/a |
| <a name="module_import_s3_bucket"></a> [import\_s3\_bucket](#module\_import\_s3\_bucket) | terraform-aws-modules/s3-bucket/aws | ~> 3.0 |
| <a name="module_vpc"></a> [vpc](#module\_vpc) | terraform-aws-modules/vpc/aws | ~> 5.0 |

## Resources

| Name | Type |
|------|------|
| [aws_iam_role.s3_import](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/iam_role) | resource |
| [aws_iam_role_policy.s3_import](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/iam_role_policy) | resource |
| [aws_availability_zones.available](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/data-sources/availability_zones) | data source |
| [aws_iam_policy_document.s3_import](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/data-sources/iam_policy_document) | data source |
| [aws_iam_policy_document.s3_import_assume](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/data-sources/iam_policy_document) | data source |

## Inputs

No inputs.

## Outputs

| Name | Description |
|------|-------------|
| <a name="output_additional_cluster_endpoints"></a> [additional\_cluster\_endpoints](#output\_additional\_cluster\_endpoints) | A map of additional cluster endpoints and their attributes |
| <a name="output_cluster_arn"></a> [cluster\_arn](#output\_cluster\_arn) | Amazon Resource Name (ARN) of cluster |
| <a name="output_cluster_database_name"></a> [cluster\_database\_name](#output\_cluster\_database\_name) | Name for an automatically created database on cluster creation |
| <a name="output_cluster_endpoint"></a> [cluster\_endpoint](#output\_cluster\_endpoint) | Writer endpoint for the cluster |
| <a name="output_cluster_engine_version_actual"></a> [cluster\_engine\_version\_actual](#output\_cluster\_engine\_version\_actual) | The running version of the cluster database |
| <a name="output_cluster_hosted_zone_id"></a> [cluster\_hosted\_zone\_id](#output\_cluster\_hosted\_zone\_id) | The Route53 Hosted Zone ID of the endpoint |
| <a name="output_cluster_id"></a> [cluster\_id](#output\_cluster\_id) | The RDS Cluster Identifier |
| <a name="output_cluster_instances"></a> [cluster\_instances](#output\_cluster\_instances) | A map of cluster instances and their attributes |
| <a name="output_cluster_master_user_secret"></a> [cluster\_master\_user\_secret](#output\_cluster\_master\_user\_secret) | The generated database master user secret when `manage_master_user_password` is set to `true` |
| <a name="output_cluster_members"></a> [cluster\_members](#output\_cluster\_members) | List of RDS Instances that are a part of this cluster |
| <a name="output_cluster_port"></a> [cluster\_port](#output\_cluster\_port) | The database port |
| <a name="output_cluster_reader_endpoint"></a> [cluster\_reader\_endpoint](#output\_cluster\_reader\_endpoint) | A read-only endpoint for the cluster, automatically load-balanced across replicas |
| <a name="output_cluster_resource_id"></a> [cluster\_resource\_id](#output\_cluster\_resource\_id) | The RDS Cluster Resource ID |
| <a name="output_cluster_role_associations"></a> [cluster\_role\_associations](#output\_cluster\_role\_associations) | A map of IAM roles associated with the cluster and their attributes |
| <a name="output_db_cluster_cloudwatch_log_groups"></a> [db\_cluster\_cloudwatch\_log\_groups](#output\_db\_cluster\_cloudwatch\_log\_groups) | Map of CloudWatch log groups created and their attributes |
| <a name="output_db_cluster_parameter_group_arn"></a> [db\_cluster\_parameter\_group\_arn](#output\_db\_cluster\_parameter\_group\_arn) | The ARN of the DB cluster parameter group created |
| <a name="output_db_cluster_parameter_group_id"></a> [db\_cluster\_parameter\_group\_id](#output\_db\_cluster\_parameter\_group\_id) | The ID of the DB cluster parameter group created |
| <a name="output_db_parameter_group_arn"></a> [db\_parameter\_group\_arn](#output\_db\_parameter\_group\_arn) | The ARN of the DB parameter group created |
| <a name="output_db_parameter_group_id"></a> [db\_parameter\_group\_id](#output\_db\_parameter\_group\_id) | The ID of the DB parameter group created |
| <a name="output_db_subnet_group_name"></a> [db\_subnet\_group\_name](#output\_db\_subnet\_group\_name) | The db subnet group name |
| <a name="output_enhanced_monitoring_iam_role_arn"></a> [enhanced\_monitoring\_iam\_role\_arn](#output\_enhanced\_monitoring\_iam\_role\_arn) | The Amazon Resource Name (ARN) specifying the enhanced monitoring role |
| <a name="output_enhanced_monitoring_iam_role_name"></a> [enhanced\_monitoring\_iam\_role\_name](#output\_enhanced\_monitoring\_iam\_role\_name) | The name of the enhanced monitoring role |
| <a name="output_enhanced_monitoring_iam_role_unique_id"></a> [enhanced\_monitoring\_iam\_role\_unique\_id](#output\_enhanced\_monitoring\_iam\_role\_unique\_id) | Stable and unique string identifying the enhanced monitoring role |
| <a name="output_security_group_id"></a> [security\_group\_id](#output\_security\_group\_id) | The security group ID of the cluster |
<!-- END_TF_DOCS -->
