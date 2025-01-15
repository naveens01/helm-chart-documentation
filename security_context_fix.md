# Security Context

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
    <tr>
        <td rowspan=22>Deployment</td>
        <td>accounts-app</td>
        <td>Yes</td>
        <td>accounts-app</td>
        <td>accounts-app:d874082</td>
        <td>Yes</td>
        <td>sagadmin</td>
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
