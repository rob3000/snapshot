This Magento shell script allows you to quickly export and import magento database from different servers.

The import additionally allows you to update/delete entries in the database, thus allowing a developer to quickly grab a snapshot of a live server database,
and have it adjusted on import to his local environemnt.

Requirements:

1. A folder named 'snapshot' in the root of your magento folder.
2. The snapshot folder should ideally be placed in .gitignore
3. SSH access to the remote server, with ssh keys insalled, to allow passwordless remote command execution.
4. mysql client installed on both local and remote

How does it work:

When run, the script makes a ssh connection to the remote server, and runs the mysql dumps locally on the remote server.
Two files are created: a structure file (with all tables) and a data file, with limited tables.
Scp is used to copy them to the snapshot folder in your magento root.
The local snaphost files can then be imported to a local database (usually the db as configured in your magento local.xml, thus replacing your local db)

If the linux [pv](http://linux.die.net/man/1/pv) tool is installed, import will show a progress bar (handy for large databases)
Commands:

--fetch [server] Do export and import in one go.  Current database will be replaced with fetched 
  
--export-remote [server]  Take snapshot of the given remote server [must be defined in snapshot.xml]
--import [server] Import the given snapshot


The snapshot.xml file:

<snapshots>
<!-- an example snapshot form a live server -->
    <live> <!-- The remote server name to use in commands - usually live, uat, staging, but can be anything -->
        <connection> <!-- The remote server database and ssh connection details. SSH keys would ideally be installed -->
                 <host><![CDATA[]]></host>
                 <ssh_port><![CDATA[]]></ssh_port>
                 <ssh_username><![CDATA[]]></ssh_username>
                 <db_username><![CDATA[]]></db_username>
                 <db_password><![CDATA[]]></db_password>
                 <dbname><![CDATA[]]></dbname>
        </connection>
        <structure> <!-- tables to ignore when dumping data -->
            <ignore_tables>importexport_importdata,dataflow_batch,dataflow_import_data,aw_core_logger,aw_hdu_attachements,aw_hdu_department,aw_hdu_gateway,aw_hdu_mailbox,aw_hdu_message,aw_hdu_proto,aw_hdu_rpattern,aw_hdu_status,aw_hdu_templates,aw_hdu_ticket,aw_hdu_ticket_flat,report_event,dataflow_batch_import,dataflow_batch_export,import_export,log_customer,log_quote,log_summary,log_summary_type,log_url,log_url_info,log_visitor,log_visitor_info,log_visitor_online,report_viewed_product_index,report_compared_product_index,report_event,index_event,enterprise_logging_event_changes,core_cache,core_cache_tag,core_session,core_cache_tag</ignore_tables>
        </structure>
        <import> <-- actions to perform when importing : think in relation of SQL queries created -->
            <core_config_data> <!-- the table to perform the actoon on -->   
                <update> <-- Type of action : usually update or delete -->
                    <where> <-- the condtion for the action -->
                        <field><![CDATA[path]]></field>
                        <value><![CDATA[web/secure/use_in_frontend]]></value>
                    </where>
                    <set> <!-- the change data -->
                        <field><![CDATA[value]]></field>
                        <value><![CDATA[0]]></value>
                    </set>
                </update
            </core_config_data>	
        </import>
    </live>

