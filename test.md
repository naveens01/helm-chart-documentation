<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Security Context Table</title>
    <style>
        table {
            width: 100%;
            border-collapse: collapse;
        }
        th, td {
            border: 1px solid #ddd;
            padding: 8px;
            text-align: center;
        }
        th {
            background-color: #f2f2f2;
        }
        th[rowspan] {
            vertical-align: middle;
        }
    </style>
</head>
<body>
    <h1>Security Context Details</h1>
    <table>
        <tbody>
            <tr>
                <th rowspan="2">Resource</th>
                <th rowspan="2">Component</th>
                <th rowspan="2">Security Context Defined</th>
                <th rowspan="2">Containers</th>
                <th rowspan="2">Image</th>
                <th rowspan="2">Container Security Context defined</th>
                <th colspan="4">Run as</th>
                <th rowspan="2">Capabilities</th>
                <th rowspan="2">Privilege Escalation</th>
                <th rowspan="2">Privileged</th>
                <th rowspan="2">Seccomp Profile</th>
                <th rowspan="2">Read-only Root Filesystem</th>
                <th rowspan="2">Comments</th>
            </tr>
            <tr>
                <th>User</th>
                <th>UID</th>
                <th>GID</th>
                <th>Non Root</th>
            </tr>
            <!-- Example rows begin -->
            <tr>
                <td rowspan="9">Deployment</td>
                <td rowspan="2">assetcatalog</td>
                <td>Yes</td>
                <td>wait-for-elasticsearch</td>
                <td>kub-devops-utility:latest</td>
                <td>Yes</td>
                <td>sagadmin</td>
                <td>1724</td>
                <td>1724</td>
                <td>TRUE</td>
                <td>drop ALL</td>
                <td>FALSE</td>
                <td>FALSE</td>
                <td>RuntimeDefault</td>
                <td>TRUE</td>
                <td>NA</td>
            </tr>
            <tr>
                <td>Yes</td>
                <td>assetcatalog</td>
                <td>asset-catalog:11.1.3.0.167</td>
                <td>Yes</td>
                <td>webmethodsadmin</td>
                <td>1724</td>
                <td>1724</td>
                <td>TRUE</td>
                <td>drop ALL</td>
                <td>FALSE</td>
                <td>FALSE</td>
                <td>RuntimeDefault</td>
                <td>FALSE</td>
                <td>readOnlyRootFilesystem is set to false as application requires permission to create log directory</td>
            </tr>
            <tr>
                <td rowspan="2">engine</td>
                <td>Yes</td>
                <td>wait-for-elasticsearch</td>
                <td>kub-devops-utility:latest</td>
                <td>Yes</td>
                <td>sagadmin</td>
                <td>1724</td>
                <td>1724</td>
                <td>TRUE</td>
                <td>drop ALL</td>
                <td>FALSE</td>
                <td>FALSE</td>
                <td>RuntimeDefault</td>
                <td>TRUE</td>
                <td>NA</td>
            </tr>
            <tr>
                <td>Yes</td>
                <td>engine</td>
                <td>engine:11.1.3.0.170</td>
                <td>Yes</td>
                <td>webmethodsadmin</td>
                <td>1724</td>
                <td>1724</td>
                <td>TRUE</td>
                <td>drop ALL</td>
                <td>FALSE</td>
                <td>FALSE</td>
                <td>RuntimeDefault</td>
                <td>FALSE</td>
                <td>readOnlyRootFilesystem is set to false as application requires permission to create log directory</td>
            </tr>
            <!-- Repeat similar structure for other rows -->
            <tr>
                <td>esexp</td>
                <td>Yes</td>
                <td>esexp</td>
                <td>elasticsearch-exporter:v1.2.1</td>
                <td>Yes</td>
                <td>webmethodsadmin</td>
                <td>1724</td>
                <td>1724</td>
                <td>TRUE</td>
                <td>drop: SETPCAP, MKNOD, AUDIT_WRITE, CHOWN, NET_RAW, DAC_OVERRIDE, FOWNER, FSETID, KILL, SETGID, SETUID, NET_BIND_SERVICE, SYS_CHROOT, SETFCAPL</td>
                <td>FALSE</td>
                <td>FALSE</td>
                <td>RuntimeDefault</td>
                <td>TRUE</td>
                <td>NA</td>
            </tr>
            <tr>
                <td rowspan="2">Statefulset</td>
                <td rowspan="2">es</td>
                <td>Yes</td>
                <td>init-sysctl</td>
                <td>alpine:latest</td>
                <td>Yes</td>
                <td>root</td>
                <td>0</td>
                <td>NA</td>
                <td>FALSE</td>
                <td>drop ALL</td>
                <td>TRUE</td>
                <td>TRUE</td>
                <td>RuntimeDefault</td>
                <td>FALSE</td>
                <td>readOnlyRootFilesystem is set to false as application requires permission to write max_map_count</td>
            </tr>
            <tr>
                <td>Yes</td>
                <td>cplane-es-sts</td>
                <td>elasticsearch:8.14.3</td>
                <td>Yes</td>
                <td>elasticsearch</td>
                <td>1000</td>
                <td>1000</td>
                <td>FALSE</td>
                <td>drop ALL, add IPC_LOCK, SYS_RESOURCE, SYS_ADMIN, DAC_OVERRIDE, DAC_READ_SEARCH</td>
                <td>FALSE</td>
                <td>FALSE</td>
                <td>RuntimeDefault</td>
                <td>FALSE</td>
                <td>readOnlyRootFilesystem is set to false as application requires permission to create directory for snapshots</td>
            </tr>
        </tbody>
    </table>
</body>
</html>
