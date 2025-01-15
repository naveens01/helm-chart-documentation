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
        <td>assetcatalog</td>
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
        <td>engine</td>
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
        <td>FALSE </td>
        <td>readOnlyRootFilesystem is set to false as application requires permission to create log directory</td>
    </tr>
           <tr>
        <td>ingress</td>
        <td>Yes</td>
        <td>ui</td>
        <td>ingress:11.1.3.0.177</td>
        <td>Yes</td>
        <td>webmethodsadmin</td>
        <td>1724</td>
        <td>1724</td>
        <td>TRUE</td>
        <td>drop ALL</td>
        <td>FALSE</td>
        <td>FALSE</td>
        <td>RuntimeDefault</td>
        <td>FALSE </td>
        <td>readOnlyRootFilesystem is set to false as application requires permission to create log directory</td>
    </tr>
       <tr>
        <td>ui</td>
        <td>Yes</td>
        <td>ui</td>
        <td>ui:11.1.3.0.438</td>
        <td>Yes</td>
        <td>webmethodsadmin</td>
        <td>1724</td>
        <td>1724</td>
        <td>TRUE</td>
        <td>drop ALL</td>
        <td>FALSE</td>
        <td>FALSE</td>
        <td>RuntimeDefault</td>
        <td>FALSE </td>
        <td>readOnlyRootFilesystem is set to false as application requires permission to create log directory</td>
    </tr>
    </tr>
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
        <td>drop:
        - SETPCAP<br></br>- MKNOD<br></br>- AUDIT_WRITE<br></br>- CHOWN<br></br>- NET_RAW<br></br>- DAC_OVERRIDE<br></br>- FOWNER<br></br>- FSETID<br></br>- KILL<br></br>- SETGID<br></br>- SETUID<br></br>- NET_BIND_SERVICE<br></br>- SYS_CHROOT<br></br>- SETFCAPL</td>
        <td>FALSE</td>
        <td>FALSE</td>  
        <td>RuntimeDefault</td>
        <td>TRUE</td>
        <td>NA</td>
    </tr>
