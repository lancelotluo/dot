# 主循环
QuicFramer::ProcessPacket主循环的逻辑如下：

<!DOCTYPE svg [<!ENTITY nbsp "&#160;">]><svg width="2781pt" height="1238pt" viewBox="0 0 2781 1238" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink">
<g id="graph0" class="graph" transform="scale(1 1) rotate(0) translate(4 1233.6313)">
<title>ProcessPacket</title>
<polygon fill="#ffffff" stroke="transparent" points="-4,4 -4,-1233.6313 2777.1737,-1233.6313 2777.1737,4 -4,4"></polygon>
<g id="clust1" class="cluster">
<title>cluster_ProcessDataPacket</title>
<polygon fill="none" stroke="#000000" points="334.1737,-8 334.1737,-987.0313 1390.1737,-987.0313 1390.1737,-8 334.1737,-8"></polygon>
</g>
<g id="clust2" class="cluster">
<title>cluster_ProcessFrameData</title>
<polygon fill="none" stroke="#000000" points="1430.1737,-223.6 1430.1737,-752.2313 2765.1737,-752.2313 2765.1737,-223.6 1430.1737,-223.6"></polygon>
</g>
<!-- visitor_&#45;&gt;OnPacket() -->
<g id="node1" class="node">
<title>visitor_-&gt;OnPacket()</title>
<ellipse fill="#00ff00" stroke="#000000" cx="93.1737" cy="-1211.6313" rx="93.3476" ry="18"></ellipse>
<text text-anchor="middle" x="93.1737" y="-1207.4313" font-family="Times,serif" font-size="14.00" fill="#000000">visitor_-&gt;OnPacket()</text>
</g>
<!-- !ProcessPublicHeader(&amp;reader, &amp;header) -->
<g id="node4" class="node">
<title>!ProcessPublicHeader(&amp;reader, &amp;header)</title>
<polygon fill="none" stroke="#000000" points="353.1737,-1156.6313 109.4566,-1138.6313 353.1737,-1120.6313 596.8907,-1138.6313 353.1737,-1156.6313"></polygon>
<text text-anchor="middle" x="353.1737" y="-1134.4313" font-family="Times,serif" font-size="14.00" fill="#000000">!ProcessPublicHeader(&amp;reader, &amp;header)</text>
</g>
<!-- visitor_&#45;&gt;OnPacket()&#45;&gt;!ProcessPublicHeader(&amp;reader, &amp;header) -->
<g id="edge2" class="edge">
<title>visitor_-&gt;OnPacket()-&gt;!ProcessPublicHeader(&amp;reader, &amp;header)</title>
<path fill="none" stroke="#ff0000" d="M146.1019,-1196.7707C188.4793,-1184.8724 247.9577,-1168.1727 292.3024,-1155.722"></path>
<polygon fill="#ff0000" stroke="#ff0000" points="293.4086,-1159.0469 302.0902,-1152.9739 291.5164,-1152.3075 293.4086,-1159.0469"></polygon>
</g>
<!-- bool QuicFramer::ProcessPacket -->
<g id="node2" class="node">
<title>bool QuicFramer::ProcessPacket</title>
<ellipse fill="#ff0000" stroke="#000000" cx="153.1737" cy="-872.2313" rx="137.7553" ry="18"></ellipse>
<text text-anchor="middle" x="153.1737" y="-868.0313" font-family="Times,serif" font-size="14.00" fill="#000000">bool QuicFramer::ProcessPacket</text>
</g>
<!-- bool QuicFramer::ProcessPacket&#45;&gt;visitor_&#45;&gt;OnPacket() -->
<g id="edge1" class="edge">
<title>bool QuicFramer::ProcessPacket-&gt;visitor_-&gt;OnPacket()</title>
<path fill="none" stroke="#ff0000" stroke-dasharray="5,2" d="M126.3635,-889.9845C105.8929,-905.8899 81.1737,-931.26 81.1737,-961.0313 81.1737,-1138.6313 81.1737,-1138.6313 81.1737,-1138.6313 81.1737,-1153.529 83.6372,-1169.9131 86.3318,-1183.3781"></path>
<polygon fill="#ff0000" stroke="#ff0000" points="82.9769,-1184.4255 88.4998,-1193.4669 89.8207,-1182.9548 82.9769,-1184.4255"></polygon>
</g>
<!-- !visitor_&#45;&gt;OnUnauthenticatedPublicHeader(header) -->
<g id="node3" class="node">
<title>!visitor_-&gt;OnUnauthenticatedPublicHeader(header)</title>
<polygon fill="#00ff00" stroke="#000000" points="447.1737,-1067.8313 146.9368,-1049.8313 447.1737,-1031.8313 747.4105,-1049.8313 447.1737,-1067.8313"></polygon>
<text text-anchor="middle" x="447.1737" y="-1045.6313" font-family="Times,serif" font-size="14.00" fill="#000000">!visitor_-&gt;OnUnauthenticatedPublicHeader(header)</text>
</g>
<!-- !visitor_&#45;&gt;OnUnauthenticatedPublicHeader(header)&#45;&gt;bool QuicFramer::ProcessPacket -->
<g id="edge5" class="edge">
<title>!visitor_-&gt;OnUnauthenticatedPublicHeader(header)-&gt;bool QuicFramer::ProcessPacket</title>
<path fill="none" stroke="#000000" stroke-dasharray="5,2" d="M375.3498,-1036.0864C337.2241,-1026.4946 290.8499,-1011.0111 254.3749,-987.0313 219.0249,-963.7911 188.4983,-925.0495 170.4705,-899.0802"></path>
<polygon fill="#000000" stroke="#000000" points="173.1402,-896.7813 164.6276,-890.4735 167.3486,-900.713 173.1402,-896.7813"></polygon>
<text text-anchor="middle" x="293.0731" y="-956.8313" font-family="Times,serif" font-size="14.00" fill="#000000">Y &nbsp;return true</text>
</g>
<!-- rv = ProcessDataPacket(&amp;reader, &amp;header, packet, buffer, kMaxPacketSize) -->
<g id="node5" class="node">
<title>rv = ProcessDataPacket(&amp;reader, &amp;header, packet, buffer, kMaxPacketSize)</title>
<ellipse fill="none" stroke="#000000" cx="650.1737" cy="-961.0313" rx="307.2388" ry="18"></ellipse>
<text text-anchor="middle" x="650.1737" y="-956.8313" font-family="Times,serif" font-size="14.00" fill="#000000">rv = ProcessDataPacket(&amp;reader, &amp;header, packet, buffer, kMaxPacketSize)</text>
</g>
<!-- !visitor_&#45;&gt;OnUnauthenticatedPublicHeader(header)&#45;&gt;rv = ProcessDataPacket(&amp;reader, &amp;header, packet, buffer, kMaxPacketSize) -->
<g id="edge6" class="edge">
<title>!visitor_-&gt;OnUnauthenticatedPublicHeader(header)-&gt;rv = ProcessDataPacket(&amp;reader, &amp;header, packet, buffer, kMaxPacketSize)</title>
<path fill="none" stroke="#ff0000" d="M483.466,-1033.9556C515.9192,-1019.7593 563.7959,-998.8162 599.9259,-983.0116"></path>
<polygon fill="#ff0000" stroke="#ff0000" points="601.552,-986.1205 609.3111,-978.9061 598.7466,-979.7073 601.552,-986.1205"></polygon>
<text text-anchor="middle" x="571.227" y="-1001.2313" font-family="Times,serif" font-size="14.00" fill="#000000">N</text>
</g>
<!-- !ProcessPublicHeader(&amp;reader, &amp;header)&#45;&gt;bool QuicFramer::ProcessPacket -->
<g id="edge3" class="edge">
<title>!ProcessPublicHeader(&amp;reader, &amp;header)-&gt;bool QuicFramer::ProcessPacket</title>
<path fill="none" stroke="#000000" stroke-dasharray="5,2" d="M286.4248,-1125.5446C230.112,-1112.9881 155.9113,-1092.347 138.1737,-1067.8313 101.9321,-1017.7409 124.6316,-940.3347 140.9044,-899.5399"></path>
<polygon fill="#000000" stroke="#000000" points="144.1668,-900.8097 144.7619,-890.2315 137.7001,-898.1298 144.1668,-900.8097"></polygon>
<text text-anchor="middle" x="177.5079" y="-1001.2313" font-family="Times,serif" font-size="14.00" fill="#000000">Y, return RaiseError</text>
</g>
<!-- !ProcessPublicHeader(&amp;reader, &amp;header)&#45;&gt;!visitor_&#45;&gt;OnUnauthenticatedPublicHeader(header) -->
<g id="edge4" class="edge">
<title>!ProcessPublicHeader(&amp;reader, &amp;header)-&gt;!visitor_-&gt;OnUnauthenticatedPublicHeader(header)</title>
<path fill="none" stroke="#ff0000" d="M370.8543,-1121.9287C385.2053,-1108.3716 405.6285,-1089.0782 421.737,-1073.8608"></path>
<polygon fill="#ff0000" stroke="#ff0000" points="424.3869,-1076.1723 429.2527,-1066.7609 419.5798,-1071.0838 424.3869,-1076.1723"></polygon>
<text text-anchor="middle" x="413.227" y="-1090.0313" font-family="Times,serif" font-size="14.00" fill="#000000">N</text>
</g>
<!-- rv = ProcessDataPacket(&amp;reader, &amp;header, packet, buffer, kMaxPacketSize)&#45;&gt;bool QuicFramer::ProcessPacket -->
<g id="edge7" class="edge">
<title>rv = ProcessDataPacket(&amp;reader, &amp;header, packet, buffer, kMaxPacketSize)-&gt;bool QuicFramer::ProcessPacket</title>
<path fill="none" stroke="#ff0000" stroke-dasharray="5,2" d="M469.5545,-946.4223C403.8351,-940.189 337.9975,-932.6121 306.5867,-925.0313 286.4751,-920.1775 282.6592,-915.1846 263.1737,-908.2313 247.8148,-902.7505 231.139,-897.1268 215.5248,-892.0015"></path>
<polygon fill="#ff0000" stroke="#ff0000" points="216.5537,-888.6557 205.9612,-888.8805 214.3819,-895.3103 216.5537,-888.6557"></polygon>
<text text-anchor="middle" x="331.4672" y="-912.4313" font-family="Times,serif" font-size="14.00" fill="#000000">return rv</text>
</g>
<!-- bool QuicFramer::ProcessDataPacket -->
<g id="node13" class="node">
<title>bool QuicFramer::ProcessDataPacket</title>
<ellipse fill="none" stroke="#000000" cx="845.1737" cy="-799.2313" rx="156.2121" ry="18"></ellipse>
<text text-anchor="middle" x="845.1737" y="-795.0313" font-family="Times,serif" font-size="14.00" fill="#000000">bool QuicFramer::ProcessDataPacket</text>
</g>
<!-- rv = ProcessDataPacket(&amp;reader, &amp;header, packet, buffer, kMaxPacketSize)&#45;&gt;bool QuicFramer::ProcessDataPacket -->
<g id="edge8" class="edge">
<title>rv = ProcessDataPacket(&amp;reader, &amp;header, packet, buffer, kMaxPacketSize)-&gt;bool QuicFramer::ProcessDataPacket</title>
<path fill="none" stroke="#ff0000" stroke-dasharray="5,2" d="M672.1636,-942.7853C707.3138,-913.6196 776.2014,-856.4606 815.6201,-823.7531"></path>
<polygon fill="#ff0000" stroke="#ff0000" points="818.1008,-826.2428 823.5616,-817.1637 813.6309,-820.8557 818.1008,-826.2428"></polygon>
</g>
<!-- !ProcessUnauthenticatedHeader(encrypted_reader, header) -->
<g id="node6" class="node">
<title>!ProcessUnauthenticatedHeader(encrypted_reader, header)</title>
<polygon fill="none" stroke="#000000" points="1032.1737,-671.2313 694.4246,-653.2313 1032.1737,-635.2313 1369.9227,-653.2313 1032.1737,-671.2313"></polygon>
<text text-anchor="middle" x="1032.1737" y="-649.0313" font-family="Times,serif" font-size="14.00" fill="#000000">!ProcessUnauthenticatedHeader(encrypted_reader, header)</text>
</g>
<!-- !ProcessUnauthenticatedHeader(encrypted_reader, header)&#45;&gt;rv = ProcessDataPacket(&amp;reader, &amp;header, packet, buffer, kMaxPacketSize) -->
<g id="edge10" class="edge">
<title>!ProcessUnauthenticatedHeader(encrypted_reader, header)-&gt;rv = ProcessDataPacket(&amp;reader, &amp;header, packet, buffer, kMaxPacketSize)</title>
<path fill="none" stroke="#000000" stroke-dasharray="5,2" d="M1034.0925,-671.2078C1035.5025,-685.9694 1037.1737,-707.4304 1037.1737,-726.2313 1037.1737,-872.2313 1037.1737,-872.2313 1037.1737,-872.2313 1037.1737,-912.4299 956.8483,-934.4319 869.3879,-946.4739"></path>
<polygon fill="#000000" stroke="#000000" points="868.6996,-943.0345 859.2483,-947.8223 869.6224,-949.9735 868.6996,-943.0345"></polygon>
<text text-anchor="middle" x="1077.4027" y="-795.0313" font-family="Times,serif" font-size="14.00" fill="#000000">Y &nbsp;return false</text>
</g>
<!-- !DecryptPayload -->
<g id="node7" class="node">
<title>!DecryptPayload</title>
<polygon fill="none" stroke="#000000" points="585.1737,-502.4 476.9054,-484.4 585.1737,-466.4 693.4419,-484.4 585.1737,-502.4"></polygon>
<text text-anchor="middle" x="585.1737" y="-480.2" font-family="Times,serif" font-size="14.00" fill="#000000">!DecryptPayload</text>
</g>
<!-- !ProcessUnauthenticatedHeader(encrypted_reader, header)&#45;&gt;!DecryptPayload -->
<g id="edge11" class="edge">
<title>!ProcessUnauthenticatedHeader(encrypted_reader, header)-&gt;!DecryptPayload</title>
<path fill="none" stroke="#ff0000" d="M910.0715,-641.7272C868.2791,-636.0461 827.6485,-628.04 810.1737,-617.2313 773.9311,-594.8143 788.586,-564.5362 754.1737,-539.4 723.8035,-517.2164 684.0409,-503.6888 650.715,-495.6035"></path>
<polygon fill="#ff0000" stroke="#ff0000" points="651.1279,-492.1068 640.5966,-493.2654 649.5519,-498.927 651.1279,-492.1068"></polygon>
<text text-anchor="middle" x="794.227" y="-564.6156" font-family="Times,serif" font-size="14.00" fill="#000000">N</text>
</g>
<!-- !ProcessAndCalculatePacketNumber -->
<g id="node10" class="node">
<title>!ProcessAndCalculatePacketNumber</title>
<polygon fill="none" stroke="#000000" points="1069.1737,-502.4 850.9598,-484.4 1069.1737,-466.4 1287.3875,-484.4 1069.1737,-502.4"></polygon>
<text text-anchor="middle" x="1069.1737" y="-480.2" font-family="Times,serif" font-size="14.00" fill="#000000">!ProcessAndCalculatePacketNumber</text>
</g>
<!-- !ProcessUnauthenticatedHeader(encrypted_reader, header)&#45;&gt;!ProcessAndCalculatePacketNumber -->
<g id="edge19" class="edge">
<title>!ProcessUnauthenticatedHeader(encrypted_reader, header)-&gt;!ProcessAndCalculatePacketNumber</title>
<path fill="none" stroke="#ff0000" stroke-dasharray="5,2" d="M1033.1572,-635.2206C1034.7104,-612.553 1038.5001,-572.5699 1047.1737,-539.4 1049.6311,-530.0023 1053.27,-520.0521 1056.884,-511.2365"></path>
<polygon fill="#ff0000" stroke="#ff0000" points="1060.1815,-512.4266 1060.8895,-501.8554 1053.7438,-509.6778 1060.1815,-512.4266"></polygon>
</g>
<!-- !DecryptPayload&#45;&gt;rv = ProcessDataPacket(&amp;reader, &amp;header, packet, buffer, kMaxPacketSize) -->
<g id="edge12" class="edge">
<title>!DecryptPayload-&gt;rv = ProcessDataPacket(&amp;reader, &amp;header, packet, buffer, kMaxPacketSize)</title>
<path fill="none" stroke="#000000" stroke-dasharray="5,2" d="M580.5895,-501.7217C576.5065,-518.6893 571.1737,-545.3437 571.1737,-568.8156 571.1737,-872.2313 571.1737,-872.2313 571.1737,-872.2313 571.1737,-898.8909 591.2955,-921.259 611.2287,-936.877"></path>
<polygon fill="#000000" stroke="#000000" points="609.2873,-939.7947 619.404,-942.9416 613.4578,-934.1727 609.2873,-939.7947"></polygon>
<text text-anchor="middle" x="611.4027" y="-722.0313" font-family="Times,serif" font-size="14.00" fill="#000000">Y &nbsp;return false</text>
</g>
<!-- !visitor_&#45;&gt;OnPacketHeader(*header) -->
<g id="node8" class="node">
<title>!visitor_-&gt;OnPacketHeader(*header)</title>
<polygon fill="#00ff00" stroke="#000000" points="581.1737,-340.6 362.2664,-322.6 581.1737,-304.6 800.0809,-322.6 581.1737,-340.6"></polygon>
<text text-anchor="middle" x="581.1737" y="-318.4" font-family="Times,serif" font-size="14.00" fill="#000000">!visitor_-&gt;OnPacketHeader(*header)</text>
</g>
<!-- !DecryptPayload&#45;&gt;!visitor_&#45;&gt;OnPacketHeader(*header) -->
<g id="edge13" class="edge">
<title>!DecryptPayload-&gt;!visitor_-&gt;OnPacketHeader(*header)</title>
<path fill="none" stroke="#ff0000" d="M584.7303,-466.4664C584.0395,-438.5238 582.7003,-384.3535 581.8768,-351.0429"></path>
<polygon fill="#ff0000" stroke="#ff0000" points="585.3652,-350.5244 581.619,-340.614 578.3673,-350.6974 585.3652,-350.5244"></polygon>
<text text-anchor="middle" x="588.227" y="-407.2" font-family="Times,serif" font-size="14.00" fill="#000000">N</text>
</g>
<!-- !visitor_&#45;&gt;OnPacketHeader(*header)&#45;&gt;rv = ProcessDataPacket(&amp;reader, &amp;header, packet, buffer, kMaxPacketSize) -->
<g id="edge14" class="edge">
<title>!visitor_-&gt;OnPacketHeader(*header)-&gt;rv = ProcessDataPacket(&amp;reader, &amp;header, packet, buffer, kMaxPacketSize)</title>
<path fill="none" stroke="#000000" stroke-dasharray="5,2" d="M531.8536,-336.6673C497.634,-349.7939 458.1737,-373.2103 458.1737,-411.4 458.1737,-872.2313 458.1737,-872.2313 458.1737,-872.2313 458.1737,-898.8532 519.7504,-923.8016 573.1603,-940.434"></path>
<polygon fill="#000000" stroke="#000000" points="572.4353,-943.872 583.0214,-943.4414 574.4773,-937.1764 572.4353,-943.872"></polygon>
<text text-anchor="middle" x="496.0731" y="-649.0313" font-family="Times,serif" font-size="14.00" fill="#000000">Y &nbsp;return true</text>
</g>
<!-- !ProcessFrameData(&amp;reader, *header) -->
<g id="node9" class="node">
<title>!ProcessFrameData(&amp;reader, *header)</title>
<polygon fill="none" stroke="#000000" points="1156.1737,-178.8 929.8612,-160.8 1156.1737,-142.8 1382.4861,-160.8 1156.1737,-178.8"></polygon>
<text text-anchor="middle" x="1156.1737" y="-156.6" font-family="Times,serif" font-size="14.00" fill="#000000">!ProcessFrameData(&amp;reader, *header)</text>
</g>
<!-- !visitor_&#45;&gt;OnPacketHeader(*header)&#45;&gt;!ProcessFrameData(&amp;reader, *header) -->
<g id="edge15" class="edge">
<title>!visitor_-&gt;OnPacketHeader(*header)-&gt;!ProcessFrameData(&amp;reader, *header)</title>
<path fill="none" stroke="#ff0000" d="M603.9614,-306.3148C644.1896,-278.5093 731.3883,-222.406 814.1737,-196.8 852.0517,-185.0841 942.6851,-175.8347 1021.1362,-169.6462"></path>
<polygon fill="#ff0000" stroke="#ff0000" points="1021.4318,-173.1339 1031.1303,-168.869 1020.889,-166.1549 1021.4318,-173.1339"></polygon>
<text text-anchor="middle" x="749.227" y="-245.4" font-family="Times,serif" font-size="14.00" fill="#000000">N</text>
</g>
<!-- !ProcessFrameData(&amp;reader, *header)&#45;&gt;rv = ProcessDataPacket(&amp;reader, &amp;header, packet, buffer, kMaxPacketSize) -->
<g id="edge16" class="edge">
<title>!ProcessFrameData(&amp;reader, *header)-&gt;rv = ProcessDataPacket(&amp;reader, &amp;header, packet, buffer, kMaxPacketSize)</title>
<path fill="none" stroke="#000000" stroke-dasharray="5,2" d="M1097.888,-174.2077C1027.1286,-193.005 907.5599,-233.17 829.1737,-304.6 815.6449,-316.9282 818.6733,-324.9549 809.1737,-340.6 764.4281,-414.2924 754.4991,-433.8815 702.1737,-502.4 688.897,-519.7854 677.4227,-518.9275 669.7157,-539.4 657.6064,-571.5662 670.1737,-582.3612 670.1737,-616.7313 670.1737,-872.2313 670.1737,-872.2313 670.1737,-872.2313 670.1737,-892.9083 664.949,-915.6463 659.8405,-932.937"></path>
<polygon fill="#000000" stroke="#000000" points="656.3594,-932.3433 656.7224,-942.9319 663.0418,-934.428 656.3594,-932.3433"></polygon>
<text text-anchor="middle" x="709.4027" y="-564.6156" font-family="Times,serif" font-size="14.00" fill="#000000">Y &nbsp;return false</text>
</g>
<!-- visitor_&#45;&gt;OnPacketComplete() -->
<g id="node12" class="node">
<title>visitor_-&gt;OnPacketComplete()</title>
<ellipse fill="#00ff00" stroke="#000000" cx="814.1737" cy="-34" rx="130.9478" ry="18"></ellipse>
<text text-anchor="middle" x="814.1737" y="-29.8" font-family="Times,serif" font-size="14.00" fill="#000000">visitor_-&gt;OnPacketComplete()</text>
</g>
<!-- !ProcessFrameData(&amp;reader, *header)&#45;&gt;visitor_&#45;&gt;OnPacketComplete() -->
<g id="edge17" class="edge">
<title>!ProcessFrameData(&amp;reader, *header)-&gt;visitor_-&gt;OnPacketComplete()</title>
<path fill="none" stroke="#ff0000" d="M1115.9362,-145.8816C1054.8042,-123.2162 938.3664,-80.0457 869.3714,-54.4651"></path>
<polygon fill="#ff0000" stroke="#ff0000" points="870.5545,-51.171 859.9615,-50.9763 868.121,-57.7344 870.5545,-51.171"></polygon>
<text text-anchor="middle" x="1007.227" y="-93.2" font-family="Times,serif" font-size="14.00" fill="#000000">N</text>
</g>
<!-- 判断stream类型 -->
<g id="node14" class="node">
<title>判断stream类型</title>
<polygon fill="none" stroke="#000000" points="1584.1737,-744.2313 1484.5613,-726.2313 1584.1737,-708.2313 1683.786,-726.2313 1584.1737,-744.2313"></polygon>
<text text-anchor="middle" x="1584.1737" y="-722.0313" font-family="Times,serif" font-size="14.00" fill="#000000">判断stream类型</text>
</g>
<!-- !ProcessFrameData(&amp;reader, *header)&#45;&gt;判断stream类型 -->
<g id="edge24" class="edge">
<title>!ProcessFrameData(&amp;reader, *header)-&gt;判断stream类型</title>
<path fill="none" stroke="#000000" stroke-dasharray="5,2" d="M1234.1082,-172.6836C1308.7726,-186.3309 1410.1737,-211.8197 1410.1737,-249.6 1410.1737,-653.2313 1410.1737,-653.2313 1410.1737,-653.2313 1410.1737,-679.3363 1477.6124,-701.2308 1528.5208,-714.0396"></path>
<polygon fill="#000000" stroke="#000000" points="1527.9111,-717.4938 1538.4572,-716.4788 1529.58,-710.6956 1527.9111,-717.4938"></polygon>
</g>
<!-- !ProcessAndCalculatePacketNumber&#45;&gt;!ProcessUnauthenticatedHeader(encrypted_reader, header) -->
<g id="edge20" class="edge">
<title>!ProcessAndCalculatePacketNumber-&gt;!ProcessUnauthenticatedHeader(encrypted_reader, header)</title>
<path fill="none" stroke="#000000" stroke-dasharray="5,2" d="M1065.8613,-502.2041C1061.6183,-524.6586 1053.8873,-564.4219 1046.1737,-598.2313 1044.1258,-607.2071 1041.7078,-616.9578 1039.4627,-625.7219"></path>
<polygon fill="#000000" stroke="#000000" points="1036.0619,-624.893 1036.939,-635.4515 1042.8377,-626.6506 1036.0619,-624.893"></polygon>
<text text-anchor="middle" x="1113.7579" y="-564.6156" font-family="Times,serif" font-size="14.00" fill="#000000">Y return RaiseError</text>
</g>
<!-- !visitor_&#45;&gt;OnUnauthenticatedHeader(*header) -->
<g id="node11" class="node">
<title>!visitor_-&gt;OnUnauthenticatedHeader(*header)</title>
<polygon fill="#00ff00" stroke="#000000" points="1110.1737,-340.6 838.0834,-322.6 1110.1737,-304.6 1382.2639,-322.6 1110.1737,-340.6"></polygon>
<text text-anchor="middle" x="1110.1737" y="-318.4" font-family="Times,serif" font-size="14.00" fill="#000000">!visitor_-&gt;OnUnauthenticatedHeader(*header)</text>
</g>
<!-- !ProcessAndCalculatePacketNumber&#45;&gt;!visitor_&#45;&gt;OnUnauthenticatedHeader(*header) -->
<g id="edge21" class="edge">
<title>!ProcessAndCalculatePacketNumber-&gt;!visitor_-&gt;OnUnauthenticatedHeader(*header)</title>
<path fill="none" stroke="#ff0000" d="M1073.718,-466.4664C1080.8294,-438.4023 1094.6449,-383.8819 1103.076,-350.6096"></path>
<polygon fill="#ff0000" stroke="#ff0000" points="1106.5453,-351.1673 1105.6089,-340.614 1099.7597,-349.4478 1106.5453,-351.1673"></polygon>
<text text-anchor="middle" x="1096.227" y="-407.2" font-family="Times,serif" font-size="14.00" fill="#000000">N</text>
</g>
<!-- !visitor_&#45;&gt;OnUnauthenticatedHeader(*header)&#45;&gt;!ProcessUnauthenticatedHeader(encrypted_reader, header) -->
<g id="edge22" class="edge">
<title>!visitor_-&gt;OnUnauthenticatedHeader(*header)-&gt;!ProcessUnauthenticatedHeader(encrypted_reader, header)</title>
<path fill="none" stroke="#000000" stroke-dasharray="5,2" d="M1007.5122,-333.8712C924.7682,-348.9654 813.4215,-384.2086 758.2157,-466.4 749.2945,-479.682 750.0753,-488.6256 758.2157,-502.4 771.3119,-524.5602 915.3403,-596.6578 989.131,-632.5473"></path>
<polygon fill="#000000" stroke="#000000" points="987.6731,-635.7301 998.1978,-636.9468 990.729,-629.4324 987.6731,-635.7301"></polygon>
<text text-anchor="middle" x="800.1527" y="-480.2" font-family="Times,serif" font-size="14.00" fill="#000000">Y, &nbsp;return false</text>
</g>
<!-- !visitor_&#45;&gt;OnUnauthenticatedHeader(*header)&#45;&gt;!ProcessUnauthenticatedHeader(encrypted_reader, header) -->
<g id="edge23" class="edge">
<title>!visitor_-&gt;OnUnauthenticatedHeader(*header)-&gt;!ProcessUnauthenticatedHeader(encrypted_reader, header)</title>
<path fill="none" stroke="#ff0000" stroke-dasharray="5,2" d="M1174.3695,-336.386C1237.0858,-351.7587 1323.1737,-378.7978 1323.1737,-411.4 1323.1737,-568.8156 1323.1737,-568.8156 1323.1737,-568.8156 1323.1737,-605.7674 1235.5318,-627.5852 1156.4428,-639.751"></path>
<polygon fill="#ff0000" stroke="#ff0000" points="1155.7222,-636.3197 1146.3493,-641.2594 1156.7569,-643.2428 1155.7222,-636.3197"></polygon>
<text text-anchor="middle" x="1352.5198" y="-480.2" font-family="Times,serif" font-size="14.00" fill="#000000">return true</text>
</g>
<!-- visitor_&#45;&gt;OnPacketComplete()&#45;&gt;rv = ProcessDataPacket(&amp;reader, &amp;header, packet, buffer, kMaxPacketSize) -->
<g id="edge18" class="edge">
<title>visitor_-&gt;OnPacketComplete()-&gt;rv = ProcessDataPacket(&amp;reader, &amp;header, packet, buffer, kMaxPacketSize)</title>
<path fill="none" stroke="#ff0000" stroke-dasharray="5,2" d="M688.2638,-38.8959C548.7422,-46.0518 343.1737,-62.5145 343.1737,-97.4 343.1737,-286.1 343.1737,-286.1 343.1737,-286.1 343.1737,-390.7287 360.1737,-416.2713 360.1737,-520.9 360.1737,-872.2313 360.1737,-872.2313 360.1737,-872.2313 360.1737,-906.7499 436.2433,-929.2034 510.4628,-942.8958"></path>
<polygon fill="#ff0000" stroke="#ff0000" points="509.9803,-946.365 520.4414,-944.6874 511.2173,-939.4752 509.9803,-946.365"></polygon>
<text text-anchor="middle" x="388.5198" y="-480.2" font-family="Times,serif" font-size="14.00" fill="#000000">return true</text>
</g>
<!-- bool QuicFramer::ProcessDataPacket&#45;&gt;!ProcessUnauthenticatedHeader(encrypted_reader, header) -->
<g id="edge9" class="edge">
<title>bool QuicFramer::ProcessDataPacket-&gt;!ProcessUnauthenticatedHeader(encrypted_reader, header)</title>
<path fill="none" stroke="#ff0000" stroke-dasharray="5,2" d="M868.1035,-781.3288C901.9237,-754.9238 965.1946,-705.5251 1002.5086,-676.3923"></path>
<polygon fill="#ff0000" stroke="#ff0000" points="1004.7771,-679.0616 1010.5054,-670.1488 1000.4693,-673.544 1004.7771,-679.0616"></polygon>
</g>
<!-- PADDING,RST_STREAM,CONNECTION_CLOSE,GOAWAY, WINDOW_UPDATE, BLOCKED, STOP_WAITING, PING
 &#160;&#160;&#160;等类型的Frame的处理逻辑类似，都会调用相应的visitor_&#45;&gt;On{type}Frame函数 -->
<g id="node17" class="node">
<title>PADDING,RST_STREAM,CONNECTION_CLOSE,GOAWAY, WINDOW_UPDATE, BLOCKED, STOP_WAITING, PING
 &nbsp;&nbsp;&nbsp;等类型的Frame的处理逻辑类似，都会调用相应的visitor_-&gt;On{type}Frame函数</title>
<ellipse fill="#00ff00" stroke="#000000" cx="2235.1737" cy="-568.8156" rx="521.6594" ry="29.3315"></ellipse>
<text text-anchor="middle" x="2235.1737" y="-573.0156" font-family="Times,serif" font-size="14.00" fill="#000000">PADDING,RST_STREAM,CONNECTION_CLOSE,GOAWAY, WINDOW_UPDATE, BLOCKED, STOP_WAITING, PING</text>
<text text-anchor="middle" x="2235.1737" y="-556.2156" font-family="Times,serif" font-size="14.00" fill="#000000"> &nbsp;&nbsp;&nbsp;等类型的Frame的处理逻辑类似，都会调用相应的visitor_-&gt;On{type}Frame函数</text>
</g>
<!-- 判断stream类型&#45;&gt;PADDING,RST_STREAM,CONNECTION_CLOSE,GOAWAY, WINDOW_UPDATE, BLOCKED, STOP_WAITING, PING
 &#160;&#160;&#160;等类型的Frame的处理逻辑类似，都会调用相应的visitor_&#45;&gt;On{type}Frame函数 -->
<g id="edge33" class="edge">
<title>判断stream类型-&gt;PADDING,RST_STREAM,CONNECTION_CLOSE,GOAWAY, WINDOW_UPDATE, BLOCKED, STOP_WAITING, PING
 &nbsp;&nbsp;&nbsp;等类型的Frame的处理逻辑类似，都会调用相应的visitor_-&gt;On{type}Frame函数</title>
<path fill="none" stroke="#000000" d="M1626.8876,-715.9028C1722.77,-692.7179 1958.303,-635.7646 2106.5553,-599.9163"></path>
<polygon fill="#000000" stroke="#000000" points="2107.7363,-603.2317 2116.6335,-597.4794 2106.091,-596.4278 2107.7363,-603.2317"></polygon>
</g>
<!-- Stream Frame -->
<g id="node18" class="node">
<title>Stream Frame</title>
<ellipse fill="none" stroke="#000000" cx="1630.1737" cy="-568.8156" rx="65.4435" ry="18"></ellipse>
<text text-anchor="middle" x="1630.1737" y="-564.6156" font-family="Times,serif" font-size="14.00" fill="#000000">Stream Frame</text>
</g>
<!-- 判断stream类型&#45;&gt;Stream Frame -->
<g id="edge25" class="edge">
<title>判断stream类型-&gt;Stream Frame</title>
<path fill="none" stroke="#000000" d="M1589.184,-709.0854C1597.0844,-682.0498 1612.5135,-629.2502 1622.0242,-596.7038"></path>
<polygon fill="#000000" stroke="#000000" points="1625.4416,-597.4871 1624.887,-586.9068 1618.7226,-595.5236 1625.4416,-597.4871"></polygon>
</g>
<!-- Ack Frame -->
<g id="node20" class="node">
<title>Ack Frame</title>
<ellipse fill="none" stroke="#000000" cx="1492.1737" cy="-568.8156" rx="54.4127" ry="18"></ellipse>
<text text-anchor="middle" x="1492.1737" y="-564.6156" font-family="Times,serif" font-size="14.00" fill="#000000">Ack Frame</text>
</g>
<!-- 判断stream类型&#45;&gt;Ack Frame -->
<g id="edge29" class="edge">
<title>判断stream类型-&gt;Ack Frame</title>
<path fill="none" stroke="#000000" d="M1574.5019,-709.6826C1558.63,-682.5251 1526.9157,-628.2606 1507.816,-595.5804"></path>
<polygon fill="#000000" stroke="#000000" points="1510.6237,-593.4479 1502.556,-586.5803 1504.5802,-596.98 1510.6237,-593.4479"></polygon>
</g>
<!-- visitor_&#45;&gt;OnStreamFrame(frame) -->
<g id="node15" class="node">
<title>visitor_-&gt;OnStreamFrame(frame)</title>
<ellipse fill="#00ff00" stroke="#000000" cx="1933.1737" cy="-249.6" rx="141.9385" ry="18"></ellipse>
<text text-anchor="middle" x="1933.1737" y="-245.4" font-family="Times,serif" font-size="14.00" fill="#000000">visitor_-&gt;OnStreamFrame(frame)</text>
</g>
<!-- visitor_&#45;&gt;OnStreamFrame(frame)&#45;&gt;!ProcessFrameData(&amp;reader, *header) -->
<g id="edge28" class="edge">
<title>visitor_-&gt;OnStreamFrame(frame)-&gt;!ProcessFrameData(&amp;reader, *header)</title>
<path fill="none" stroke="#000000" stroke-dasharray="5,2" d="M1841.6199,-235.7758C1764.1216,-224.3628 1650.0357,-208.2396 1550.1737,-196.8 1459.8837,-186.457 1357.6793,-177.189 1279.994,-170.6476"></path>
<polygon fill="#000000" stroke="#000000" points="1279.9393,-167.1308 1269.6819,-169.7831 1279.3545,-174.1063 1279.9393,-167.1308"></polygon>
<text text-anchor="middle" x="1705.5198" y="-201" font-family="Times,serif" font-size="14.00" fill="#000000">return true</text>
</g>
<!-- visitor_&#45;&gt;OnAckFrame(frame) -->
<g id="node16" class="node">
<title>visitor_-&gt;OnAckFrame(frame)</title>
<ellipse fill="#00ff00" stroke="#000000" cx="1601.1737" cy="-249.6" rx="130.9072" ry="18"></ellipse>
<text text-anchor="middle" x="1601.1737" y="-245.4" font-family="Times,serif" font-size="14.00" fill="#000000">visitor_-&gt;OnAckFrame(frame)</text>
</g>
<!-- visitor_&#45;&gt;OnAckFrame(frame)&#45;&gt;!ProcessFrameData(&amp;reader, *header) -->
<g id="edge32" class="edge">
<title>visitor_-&gt;OnAckFrame(frame)-&gt;!ProcessFrameData(&amp;reader, *header)</title>
<path fill="none" stroke="#000000" stroke-dasharray="5,2" d="M1552.4953,-232.8365C1517.2351,-221.2471 1468.2222,-206.2676 1424.1737,-196.8 1373.504,-185.9093 1316.6965,-177.7167 1268.4678,-171.9206"></path>
<polygon fill="#000000" stroke="#000000" points="1268.6537,-168.4182 1258.3119,-170.72 1267.8318,-175.3698 1268.6537,-168.4182"></polygon>
<text text-anchor="middle" x="1516.5198" y="-201" font-family="Times,serif" font-size="14.00" fill="#000000">return true</text>
</g>
<!-- PADDING,RST_STREAM,CONNECTION_CLOSE,GOAWAY, WINDOW_UPDATE, BLOCKED, STOP_WAITING, PING
 &#160;&#160;&#160;等类型的Frame的处理逻辑类似，都会调用相应的visitor_&#45;&gt;On{type}Frame函数&#45;&gt;!ProcessFrameData(&amp;reader, *header) -->
<g id="edge34" class="edge">
<title>PADDING,RST_STREAM,CONNECTION_CLOSE,GOAWAY, WINDOW_UPDATE, BLOCKED, STOP_WAITING, PING
 &nbsp;&nbsp;&nbsp;等类型的Frame的处理逻辑类似，都会调用相应的visitor_-&gt;On{type}Frame函数-&gt;!ProcessFrameData(&amp;reader, *header)</title>
<path fill="none" stroke="#000000" stroke-dasharray="5,2" d="M2265.0813,-539.0872C2277.1031,-524.0188 2288.1737,-504.6464 2288.1737,-484.4 2288.1737,-484.4 2288.1737,-484.4 2288.1737,-249.6 2288.1737,-202.3244 1652.3904,-176.0342 1333.7746,-165.8273"></path>
<polygon fill="#000000" stroke="#000000" points="1333.6149,-162.3206 1323.5086,-165.5005 1333.3921,-169.317 1333.6149,-162.3206"></polygon>
<text text-anchor="middle" x="2317.5198" y="-362.8" font-family="Times,serif" font-size="14.00" fill="#000000">return true</text>
</g>
<!-- ProcessStreamFrame(reader, frame_type, &amp;frame) -->
<g id="node19" class="node">
<title>ProcessStreamFrame(reader, frame_type, &amp;frame)</title>
<ellipse fill="none" stroke="#000000" cx="2053.1737" cy="-411.4" rx="206.5137" ry="18"></ellipse>
<text text-anchor="middle" x="2053.1737" y="-407.2" font-family="Times,serif" font-size="14.00" fill="#000000">ProcessStreamFrame(reader, frame_type, &amp;frame)</text>
</g>
<!-- Stream Frame&#45;&gt;ProcessStreamFrame(reader, frame_type, &amp;frame) -->
<g id="edge26" class="edge">
<title>Stream Frame-&gt;ProcessStreamFrame(reader, frame_type, &amp;frame)</title>
<path fill="none" stroke="#000000" d="M1667.3636,-553.8066C1679.1129,-549.1255 1692.1631,-543.9887 1704.1737,-539.4 1805.9437,-500.5181 1925.2512,-457.2569 1994.7149,-432.2988"></path>
<polygon fill="#000000" stroke="#000000" points="1995.9497,-435.5743 2004.1787,-428.901 1993.5842,-428.9861 1995.9497,-435.5743"></polygon>
</g>
<!-- ProcessStreamFrame(reader, frame_type, &amp;frame)&#45;&gt;visitor_&#45;&gt;OnStreamFrame(frame) -->
<g id="edge27" class="edge">
<title>ProcessStreamFrame(reader, frame_type, &amp;frame)-&gt;visitor_-&gt;OnStreamFrame(frame)</title>
<path fill="none" stroke="#000000" d="M2039.6414,-393.154C2018.3725,-364.4764 1977.0313,-308.7347 1952.5934,-275.7843"></path>
<polygon fill="#000000" stroke="#000000" points="1955.2416,-273.4795 1946.4733,-267.5324 1949.6192,-277.6495 1955.2416,-273.4795"></polygon>
</g>
<!-- ProcessAckFrame(reader, frame_type, &amp;frame) -->
<g id="node21" class="node">
<title>ProcessAckFrame(reader, frame_type, &amp;frame)</title>
<ellipse fill="none" stroke="#000000" cx="1633.1737" cy="-411.4" rx="195.4818" ry="18"></ellipse>
<text text-anchor="middle" x="1633.1737" y="-407.2" font-family="Times,serif" font-size="14.00" fill="#000000">ProcessAckFrame(reader, frame_type, &amp;frame)</text>
</g>
<!-- Ack Frame&#45;&gt;ProcessAckFrame(reader, frame_type, &amp;frame) -->
<g id="edge30" class="edge">
<title>Ack Frame-&gt;ProcessAckFrame(reader, frame_type, &amp;frame)</title>
<path fill="none" stroke="#000000" d="M1507.8018,-551.368C1532.6553,-523.621 1581.319,-469.2918 1610.1626,-437.0901"></path>
<polygon fill="#000000" stroke="#000000" points="1612.9772,-439.1936 1617.0421,-429.4096 1607.763,-434.5232 1612.9772,-439.1936"></polygon>
</g>
<!-- ProcessAckFrame(reader, frame_type, &amp;frame)&#45;&gt;visitor_&#45;&gt;OnAckFrame(frame) -->
<g id="edge31" class="edge">
<title>ProcessAckFrame(reader, frame_type, &amp;frame)-&gt;visitor_-&gt;OnAckFrame(frame)</title>
<path fill="none" stroke="#000000" d="M1629.565,-393.154C1624.0194,-365.1137 1613.3563,-311.1988 1606.7933,-278.0143"></path>
<polygon fill="#000000" stroke="#000000" points="1610.1919,-277.1585 1604.8182,-268.0276 1603.3249,-278.5166 1610.1919,-277.1585"></polygon>
</g>
</g>
</svg>

阅读方式说明下：
1. 红色框为入口函数，绿色框为重点函数说明。
2. 虚线箭头表示函数调用关系，方向分别代表为进入和返回。
3. 实现箭头表示顺序执行关系。


# 多次循环
quic_dispatcher负责驱动整个QUIC流程，通过在QuicFramer::ProcessPacket大循环中设置不同的visitor_来实现。所以就涉及到多次循环。分别解释一下：
## 握手过程中的循环
1. 接收第一个握手消息时visitor_为quic_dispatcher，但是执行到QuicDispatcher::MaybeRejectStatelessly会调用Extract函数，该函数会重新调用一次ProcessPacket，相当于在QuicDispatcher中调用了一次循环，此时设置visitor_为ChloFramerVisitor。
```
ChloFramerVisitor visitor(&framer, delegate);
framer.set_visitor(&visitor);
if (!framer.ProcessPacket(packet)) {
  return false;
}
```
ChloFramerVisitor的工作比较简单，就是解析Chlo消息。如果找到了Chlo就返回True。
它的主要流程如下：

<svg width="958pt" height="1948pt" viewBox="0 0 958 1948" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink">
<g id="graph0" class="graph" transform="scale(1 1) rotate(0) translate(4 1944)">
<title>%0</title>
<polygon fill="#ffffff" stroke="transparent" points="-4,4 -4,-1944 953.6566,-1944 953.6566,4 -4,4"></polygon>
<!-- bool ChloExtractor::Extract -->
<g id="node1" class="node">
<title>bool ChloExtractor::Extract</title>
<ellipse fill="#ff0000" stroke="#000000" cx="676.8545" cy="-1922" rx="118.6564" ry="18"></ellipse>
<text text-anchor="middle" x="676.8545" y="-1917.8" font-family="Times,serif" font-size="14.00" fill="#000000">bool ChloExtractor::Extract</text>
</g>
<!-- ChloFramerVisitor visitor(&amp;framer, delegate) -->
<g id="node3" class="node">
<title>ChloFramerVisitor visitor(&amp;framer, delegate)</title>
<ellipse fill="none" stroke="#000000" cx="460.8545" cy="-1849" rx="188.072" ry="18"></ellipse>
<text text-anchor="middle" x="460.8545" y="-1844.8" font-family="Times,serif" font-size="14.00" fill="#000000">ChloFramerVisitor visitor(&amp;framer, delegate)</text>
</g>
<!-- bool ChloExtractor::Extract&#45;&gt;ChloFramerVisitor visitor(&amp;framer, delegate) -->
<g id="edge1" class="edge">
<title>bool ChloExtractor::Extract-&gt;ChloFramerVisitor visitor(&amp;framer, delegate)</title>
<path fill="none" stroke="#000000" stroke-dasharray="5,2" d="M628.1106,-1905.5264C596.7407,-1894.9245 555.7104,-1881.0578 522.1397,-1869.7121"></path>
<polygon fill="#000000" stroke="#000000" points="523.0269,-1866.3175 512.4326,-1866.4315 520.7856,-1872.9491 523.0269,-1866.3175"></polygon>
</g>
<!-- !framer.ProcessPacket(packet) -->
<g id="node2" class="node">
<title>!framer.ProcessPacket(packet)</title>
<polygon fill="none" stroke="#000000" points="676.8545,-1705.2 494.2444,-1687.2 676.8545,-1669.2 859.4646,-1687.2 676.8545,-1705.2"></polygon>
<text text-anchor="middle" x="676.8545" y="-1683" font-family="Times,serif" font-size="14.00" fill="#000000">!framer.ProcessPacket(packet)</text>
</g>
<!-- !framer.ProcessPacket(packet)&#45;&gt;bool ChloExtractor::Extract -->
<g id="edge4" class="edge">
<title>!framer.ProcessPacket(packet)-&gt;bool ChloExtractor::Extract</title>
<path fill="none" stroke="#000000" stroke-dasharray="5,2" d="M676.8545,-1705.2959C676.8545,-1745.9875 676.8545,-1844.9562 676.8545,-1893.7922"></path>
<polygon fill="#000000" stroke="#000000" points="673.3546,-1893.9655 676.8545,-1903.9655 680.3546,-1893.9655 673.3546,-1893.9655"></polygon>
<text text-anchor="middle" x="717.0835" y="-1800.4" font-family="Times,serif" font-size="14.00" fill="#000000">Y, return false</text>
</g>
<!-- !framer.ProcessPacket(packet)&#45;&gt;bool ChloExtractor::Extract -->
<g id="edge5" class="edge">
<title>!framer.ProcessPacket(packet)-&gt;bool ChloExtractor::Extract</title>
<path fill="none" stroke="#000000" stroke-dasharray="5,2" d="M694.5774,-1703.6545C714.4166,-1723.4051 745.4122,-1758.5963 757.8545,-1796.2 770.7028,-1835.0306 737.1031,-1873.6854 709.3321,-1897.6908"></path>
<polygon fill="#000000" stroke="#000000" points="706.7721,-1895.2679 701.3287,-1904.3575 711.2523,-1900.6464 706.7721,-1895.2679"></polygon>
<text text-anchor="middle" x="844.053" y="-1800.4" font-family="Times,serif" font-size="14.00" fill="#000000">N, return visitor.found_chlo()</text>
</g>
<!-- bool QuicFramer::ProcessPacket(const QuicEncryptedPacket&amp; packet) -->
<g id="node5" class="node">
<title>bool QuicFramer::ProcessPacket(const QuicEncryptedPacket&amp; packet)</title>
<ellipse fill="none" stroke="#000000" cx="519.8545" cy="-1614.2" rx="286.3549" ry="18"></ellipse>
<text text-anchor="middle" x="519.8545" y="-1610" font-family="Times,serif" font-size="14.00" fill="#000000">bool QuicFramer::ProcessPacket(const QuicEncryptedPacket&amp; packet)</text>
</g>
<!-- !framer.ProcessPacket(packet)&#45;&gt;bool QuicFramer::ProcessPacket(const QuicEncryptedPacket&amp; packet) -->
<g id="edge6" class="edge">
<title>!framer.ProcessPacket(packet)-&gt;bool QuicFramer::ProcessPacket(const QuicEncryptedPacket&amp; packet)</title>
<path fill="none" stroke="#000000" stroke-dasharray="5,2" d="M644.7044,-1672.2512C622.5102,-1661.9316 592.6148,-1648.0312 567.6423,-1636.4198"></path>
<polygon fill="#000000" stroke="#000000" points="568.8822,-1633.1365 558.3387,-1632.0939 565.9308,-1639.4839 568.8822,-1633.1365"></polygon>
</g>
<!-- framer.set_visitor(&amp;visitor) -->
<g id="node4" class="node">
<title>framer.set_visitor(&amp;visitor)</title>
<ellipse fill="none" stroke="#000000" cx="513.8545" cy="-1760.2" rx="118.0671" ry="18"></ellipse>
<text text-anchor="middle" x="513.8545" y="-1756" font-family="Times,serif" font-size="14.00" fill="#000000">framer.set_visitor(&amp;visitor)</text>
</g>
<!-- ChloFramerVisitor visitor(&amp;framer, delegate)&#45;&gt;framer.set_visitor(&amp;visitor) -->
<g id="edge2" class="edge">
<title>ChloFramerVisitor visitor(&amp;framer, delegate)-&gt;framer.set_visitor(&amp;visitor)</title>
<path fill="none" stroke="#000000" d="M471.8361,-1830.6006C479.3475,-1818.0155 489.4273,-1801.1271 497.8314,-1787.0463"></path>
<polygon fill="#000000" stroke="#000000" points="500.9486,-1788.6527 503.0683,-1778.272 494.9378,-1785.0651 500.9486,-1788.6527"></polygon>
</g>
<!-- framer.set_visitor(&amp;visitor)&#45;&gt;!framer.ProcessPacket(packet) -->
<g id="edge3" class="edge">
<title>framer.set_visitor(&amp;visitor)-&gt;!framer.ProcessPacket(packet)</title>
<path fill="none" stroke="#000000" d="M552.0708,-1743.0847C576.7684,-1732.0238 608.9367,-1717.6172 634.4259,-1706.2018"></path>
<polygon fill="#000000" stroke="#000000" points="636.1424,-1709.2681 643.8383,-1701.9864 633.2812,-1702.8795 636.1424,-1709.2681"></polygon>
</g>
<!-- visitor_&#45;&gt;OnPacket() visitor_=ChloFramerVisitor -->
<g id="node6" class="node">
<title>visitor_-&gt;OnPacket() visitor_=ChloFramerVisitor</title>
<ellipse fill="none" stroke="#000000" cx="494.8545" cy="-1541.2" rx="204.0514" ry="18"></ellipse>
<text text-anchor="middle" x="494.8545" y="-1537" font-family="Times,serif" font-size="14.00" fill="#000000">visitor_-&gt;OnPacket() visitor_=ChloFramerVisitor</text>
</g>
<!-- bool QuicFramer::ProcessPacket(const QuicEncryptedPacket&amp; packet)&#45;&gt;visitor_&#45;&gt;OnPacket() visitor_=ChloFramerVisitor -->
<g id="edge7" class="edge">
<title>bool QuicFramer::ProcessPacket(const QuicEncryptedPacket&amp; packet)-&gt;visitor_-&gt;OnPacket() visitor_=ChloFramerVisitor</title>
<path fill="none" stroke="#000000" d="M513.6747,-1596.1551C510.8502,-1587.9074 507.4413,-1577.9536 504.3052,-1568.7962"></path>
<polygon fill="#000000" stroke="#000000" points="507.601,-1567.617 501.0498,-1559.2904 500.9786,-1569.885 507.601,-1567.617"></polygon>
</g>
<!-- ProcessPublicHeader(&amp;reader, &amp;header) -->
<g id="node7" class="node">
<title>ProcessPublicHeader(&amp;reader, &amp;header)</title>
<ellipse fill="none" stroke="#000000" cx="488.8545" cy="-1452.4" rx="169.4387" ry="18"></ellipse>
<text text-anchor="middle" x="488.8545" y="-1448.2" font-family="Times,serif" font-size="14.00" fill="#000000">ProcessPublicHeader(&amp;reader, &amp;header)</text>
</g>
<!-- visitor_&#45;&gt;OnPacket() visitor_=ChloFramerVisitor&#45;&gt;ProcessPublicHeader(&amp;reader, &amp;header) -->
<g id="edge8" class="edge">
<title>visitor_-&gt;OnPacket() visitor_=ChloFramerVisitor-&gt;ProcessPublicHeader(&amp;reader, &amp;header)</title>
<path fill="none" stroke="#000000" d="M493.6113,-1522.8006C492.7852,-1510.575 491.6848,-1494.2887 490.7504,-1480.4599"></path>
<polygon fill="#000000" stroke="#000000" points="494.2419,-1480.2133 490.0756,-1470.472 487.2578,-1480.6852 494.2419,-1480.2133"></polygon>
</g>
<!-- visitor_&#45;&gt;OnUnauthenticatedPublicHeader(header) visitor_=ChloFramerVisitor -->
<g id="node8" class="node">
<title>visitor_-&gt;OnUnauthenticatedPublicHeader(header) visitor_=ChloFramerVisitor</title>
<ellipse fill="none" stroke="#000000" cx="486.8545" cy="-1379.4" rx="319.7062" ry="18"></ellipse>
<text text-anchor="middle" x="486.8545" y="-1375.2" font-family="Times,serif" font-size="14.00" fill="#000000">visitor_-&gt;OnUnauthenticatedPublicHeader(header) visitor_=ChloFramerVisitor</text>
</g>
<!-- ProcessPublicHeader(&amp;reader, &amp;header)&#45;&gt;visitor_&#45;&gt;OnUnauthenticatedPublicHeader(header) visitor_=ChloFramerVisitor -->
<g id="edge9" class="edge">
<title>ProcessPublicHeader(&amp;reader, &amp;header)-&gt;visitor_-&gt;OnUnauthenticatedPublicHeader(header) visitor_=ChloFramerVisitor</title>
<path fill="none" stroke="#000000" d="M488.3601,-1434.3551C488.139,-1426.2828 487.873,-1416.5764 487.6266,-1407.5817"></path>
<polygon fill="#000000" stroke="#000000" points="491.1228,-1407.3907 487.3501,-1397.4904 484.1254,-1407.5825 491.1228,-1407.3907"></polygon>
</g>
<!-- ProcessDataPacket(&amp;reader, &amp;header, packet, buffer, kMaxPacketSize) -->
<g id="node9" class="node">
<title>ProcessDataPacket(&amp;reader, &amp;header, packet, buffer, kMaxPacketSize)</title>
<ellipse fill="none" stroke="#000000" cx="660.8545" cy="-1306.4" rx="288.6043" ry="18"></ellipse>
<text text-anchor="middle" x="660.8545" y="-1302.2" font-family="Times,serif" font-size="14.00" fill="#000000">ProcessDataPacket(&amp;reader, &amp;header, packet, buffer, kMaxPacketSize)</text>
</g>
<!-- visitor_&#45;&gt;OnUnauthenticatedPublicHeader(header) visitor_=ChloFramerVisitor&#45;&gt;ProcessDataPacket(&amp;reader, &amp;header, packet, buffer, kMaxPacketSize) -->
<g id="edge10" class="edge">
<title>visitor_-&gt;OnUnauthenticatedPublicHeader(header) visitor_=ChloFramerVisitor-&gt;ProcessDataPacket(&amp;reader, &amp;header, packet, buffer, kMaxPacketSize)</title>
<path fill="none" stroke="#000000" d="M529.4197,-1361.5422C553.3288,-1351.5114 583.3581,-1338.9128 608.6757,-1328.2911"></path>
<polygon fill="#000000" stroke="#000000" points="610.2584,-1331.4227 618.1257,-1324.3265 607.5503,-1324.9678 610.2584,-1331.4227"></polygon>
</g>
<!-- ProcessDataPacket(&amp;reader, &amp;header, packet, buffer, kMaxPacketSize)&#45;&gt;!framer.ProcessPacket(packet) -->
<g id="edge11" class="edge">
<title>ProcessDataPacket(&amp;reader, &amp;header, packet, buffer, kMaxPacketSize)-&gt;!framer.ProcessPacket(packet)</title>
<path fill="none" stroke="#000000" stroke-dasharray="5,2" d="M736.1614,-1323.7981C783.187,-1336.9268 834.8545,-1356.5505 834.8545,-1379.4 834.8545,-1614.2 834.8545,-1614.2 834.8545,-1614.2 834.8545,-1636.5802 781.953,-1657.5023 737.3714,-1671.1603"></path>
<polygon fill="#000000" stroke="#000000" points="736.2946,-1667.8291 727.7164,-1674.0472 738.3,-1674.5357 736.2946,-1667.8291"></polygon>
<text text-anchor="middle" x="859.148" y="-1492.6" font-family="Times,serif" font-size="14.00" fill="#000000">return rv</text>
</g>
<!-- ProcessUnauthenticatedHeader(encrypted_reader, header) -->
<g id="node10" class="node">
<title>ProcessUnauthenticatedHeader(encrypted_reader, header)</title>
<ellipse fill="none" stroke="#000000" cx="528.8545" cy="-1233.4" rx="235.8973" ry="18"></ellipse>
<text text-anchor="middle" x="528.8545" y="-1229.2" font-family="Times,serif" font-size="14.00" fill="#000000">ProcessUnauthenticatedHeader(encrypted_reader, header)</text>
</g>
<!-- ProcessDataPacket(&amp;reader, &amp;header, packet, buffer, kMaxPacketSize)&#45;&gt;ProcessUnauthenticatedHeader(encrypted_reader, header) -->
<g id="edge12" class="edge">
<title>ProcessDataPacket(&amp;reader, &amp;header, packet, buffer, kMaxPacketSize)-&gt;ProcessUnauthenticatedHeader(encrypted_reader, header)</title>
<path fill="none" stroke="#000000" stroke-dasharray="5,2" d="M628.2253,-1288.3551C610.6419,-1278.6309 588.7703,-1266.5352 570.0263,-1256.1692"></path>
<polygon fill="#000000" stroke="#000000" points="571.5532,-1253.0141 561.1084,-1251.2374 568.1655,-1259.1398 571.5532,-1253.0141"></polygon>
</g>
<!-- DecryptPayload -->
<g id="node11" class="node">
<title>DecryptPayload</title>
<ellipse fill="none" stroke="#000000" cx="570.8545" cy="-1160.4" rx="73.4959" ry="18"></ellipse>
<text text-anchor="middle" x="570.8545" y="-1156.2" font-family="Times,serif" font-size="14.00" fill="#000000">DecryptPayload</text>
</g>
<!-- ProcessUnauthenticatedHeader(encrypted_reader, header)&#45;&gt;DecryptPayload -->
<g id="edge13" class="edge">
<title>ProcessUnauthenticatedHeader(encrypted_reader, header)-&gt;DecryptPayload</title>
<path fill="none" stroke="#000000" d="M539.2365,-1215.3551C544.17,-1206.7801 550.1646,-1196.3611 555.6021,-1186.9102"></path>
<polygon fill="#000000" stroke="#000000" points="558.6386,-1188.6506 560.5919,-1178.2374 552.5712,-1185.1597 558.6386,-1188.6506"></polygon>
</g>
<!-- ProcessFrameData(&amp;reader, *header) -->
<g id="node12" class="node">
<title>ProcessFrameData(&amp;reader, *header)</title>
<ellipse fill="none" stroke="#000000" cx="578.8545" cy="-1087.4" rx="156.7573" ry="18"></ellipse>
<text text-anchor="middle" x="578.8545" y="-1083.2" font-family="Times,serif" font-size="14.00" fill="#000000">ProcessFrameData(&amp;reader, *header)</text>
</g>
<!-- DecryptPayload&#45;&gt;ProcessFrameData(&amp;reader, *header) -->
<g id="edge14" class="edge">
<title>DecryptPayload-&gt;ProcessFrameData(&amp;reader, *header)</title>
<path fill="none" stroke="#000000" d="M572.832,-1142.3551C573.7167,-1134.2828 574.7804,-1124.5764 575.7661,-1115.5817"></path>
<polygon fill="#000000" stroke="#000000" points="579.2617,-1115.8122 576.872,-1105.4904 572.3034,-1115.0495 579.2617,-1115.8122"></polygon>
</g>
<!-- visitor_&#45;&gt;OnPacketComplete() -->
<g id="node13" class="node">
<title>visitor_-&gt;OnPacketComplete()</title>
<ellipse fill="none" stroke="#000000" cx="791.8545" cy="-1014.4" rx="130.9478" ry="18"></ellipse>
<text text-anchor="middle" x="791.8545" y="-1010.2" font-family="Times,serif" font-size="14.00" fill="#000000">visitor_-&gt;OnPacketComplete()</text>
</g>
<!-- ProcessFrameData(&amp;reader, *header)&#45;&gt;visitor_&#45;&gt;OnPacketComplete() -->
<g id="edge15" class="edge">
<title>ProcessFrameData(&amp;reader, *header)-&gt;visitor_-&gt;OnPacketComplete()</title>
<path fill="none" stroke="#000000" d="M628.7935,-1070.2847C660.1296,-1059.5452 700.6689,-1045.6514 733.4865,-1034.4041"></path>
<polygon fill="#000000" stroke="#000000" points="734.6379,-1037.7094 742.9629,-1031.1563 732.3683,-1031.0875 734.6379,-1037.7094"></polygon>
</g>
<!-- ProcessStreamFrame(reader, frame_type, &amp;frame) -->
<g id="node14" class="node">
<title>ProcessStreamFrame(reader, frame_type, &amp;frame)</title>
<ellipse fill="none" stroke="#000000" cx="337.8545" cy="-1014.4" rx="206.5137" ry="18"></ellipse>
<text text-anchor="middle" x="337.8545" y="-1010.2" font-family="Times,serif" font-size="14.00" fill="#000000">ProcessStreamFrame(reader, frame_type, &amp;frame)</text>
</g>
<!-- ProcessFrameData(&amp;reader, *header)&#45;&gt;ProcessStreamFrame(reader, frame_type, &amp;frame) -->
<g id="edge17" class="edge">
<title>ProcessFrameData(&amp;reader, *header)-&gt;ProcessStreamFrame(reader, frame_type, &amp;frame)</title>
<path fill="none" stroke="#000000" stroke-dasharray="5,2" d="M523.2616,-1070.5607C488.0421,-1059.8925 442.2784,-1046.0305 405.03,-1034.7478"></path>
<polygon fill="#000000" stroke="#000000" points="405.8471,-1031.3383 395.2619,-1031.789 403.8178,-1038.0377 405.8471,-1031.3383"></polygon>
</g>
<!-- visitor_&#45;&gt;OnPacketComplete()&#45;&gt;ProcessDataPacket(&amp;reader, &amp;header, packet, buffer, kMaxPacketSize) -->
<g id="edge16" class="edge">
<title>visitor_-&gt;OnPacketComplete()-&gt;ProcessDataPacket(&amp;reader, &amp;header, packet, buffer, kMaxPacketSize)</title>
<path fill="none" stroke="#000000" stroke-dasharray="5,2" d="M792.244,-1032.6994C792.5248,-1047.4636 792.8545,-1068.768 792.8545,-1087.4 792.8545,-1233.4 792.8545,-1233.4 792.8545,-1233.4 792.8545,-1257.6824 775.537,-1273.9515 753.4471,-1284.8264"></path>
<polygon fill="#000000" stroke="#000000" points="751.7745,-1281.7404 744.0776,-1289.0211 754.6349,-1288.1294 751.7745,-1281.7404"></polygon>
<text text-anchor="middle" x="822.2006" y="-1156.2" font-family="Times,serif" font-size="14.00" fill="#000000">return true</text>
</g>
<!-- visitor_&#45;&gt;OnStreamFrame(frame) -->
<g id="node15" class="node">
<title>visitor_-&gt;OnStreamFrame(frame)</title>
<ellipse fill="none" stroke="#000000" cx="513.8545" cy="-941.4" rx="141.9385" ry="18"></ellipse>
<text text-anchor="middle" x="513.8545" y="-937.2" font-family="Times,serif" font-size="14.00" fill="#000000">visitor_-&gt;OnStreamFrame(frame)</text>
</g>
<!-- ProcessStreamFrame(reader, frame_type, &amp;frame)&#45;&gt;visitor_&#45;&gt;OnStreamFrame(frame) -->
<g id="edge18" class="edge">
<title>ProcessStreamFrame(reader, frame_type, &amp;frame)-&gt;visitor_-&gt;OnStreamFrame(frame)</title>
<path fill="none" stroke="#000000" d="M380.4592,-996.7287C405.2854,-986.4315 436.7495,-973.381 462.8842,-962.5411"></path>
<polygon fill="#000000" stroke="#000000" points="464.3535,-965.7209 472.2495,-958.6566 461.6715,-959.255 464.3535,-965.7209"></polygon>
</g>
<!-- visitor_&#45;&gt;OnStreamFrame(frame)&#45;&gt;ProcessFrameData(&amp;reader, *header) -->
<g id="edge19" class="edge">
<title>visitor_-&gt;OnStreamFrame(frame)-&gt;ProcessFrameData(&amp;reader, *header)</title>
<path fill="none" stroke="#000000" stroke-dasharray="5,2" d="M529.7287,-959.666C537.9237,-969.8909 547.5068,-983.2072 553.8545,-996.4 563.5193,-1016.487 569.9725,-1040.9262 573.8979,-1059.418"></path>
<polygon fill="#000000" stroke="#000000" points="570.4767,-1060.1588 575.8725,-1069.2767 577.3403,-1058.784 570.4767,-1060.1588"></polygon>
<text text-anchor="middle" x="597.2006" y="-1010.2" font-family="Times,serif" font-size="14.00" fill="#000000">return true</text>
</g>
<!-- bool ChloFramerVisitor::OnStreamFrame(const QuicStreamFrame&amp; frame) -->
<g id="node16" class="node">
<title>bool ChloFramerVisitor::OnStreamFrame(const QuicStreamFrame&amp; frame)</title>
<ellipse fill="none" stroke="#000000" cx="346.8545" cy="-868.4" rx="305.5061" ry="18"></ellipse>
<text text-anchor="middle" x="346.8545" y="-864.2" font-family="Times,serif" font-size="14.00" fill="#000000">bool ChloFramerVisitor::OnStreamFrame(const QuicStreamFrame&amp; frame)</text>
</g>
<!-- visitor_&#45;&gt;OnStreamFrame(frame)&#45;&gt;bool ChloFramerVisitor::OnStreamFrame(const QuicStreamFrame&amp; frame) -->
<g id="edge20" class="edge">
<title>visitor_-&gt;OnStreamFrame(frame)-&gt;bool ChloFramerVisitor::OnStreamFrame(const QuicStreamFrame&amp; frame)</title>
<path fill="none" stroke="#000000" stroke-dasharray="5,2" d="M474.2778,-924.1C451.1855,-914.0058 421.8277,-901.1727 397.1131,-890.3693"></path>
<polygon fill="#000000" stroke="#000000" points="398.4558,-887.1365 387.8911,-886.3381 395.652,-893.5505 398.4558,-887.1365"></polygon>
</g>
<!-- crypto_framer.set_visitor(this) -->
<g id="node17" class="node">
<title>crypto_framer.set_visitor(this)</title>
<ellipse fill="none" stroke="#000000" cx="479.8545" cy="-779.6" rx="129.0874" ry="18"></ellipse>
<text text-anchor="middle" x="479.8545" y="-775.4" font-family="Times,serif" font-size="14.00" fill="#000000">crypto_framer.set_visitor(this)</text>
</g>
<!-- bool ChloFramerVisitor::OnStreamFrame(const QuicStreamFrame&amp; frame)&#45;&gt;crypto_framer.set_visitor(this) -->
<g id="edge21" class="edge">
<title>bool ChloFramerVisitor::OnStreamFrame(const QuicStreamFrame&amp; frame)-&gt;crypto_framer.set_visitor(this)</title>
<path fill="none" stroke="#000000" stroke-dasharray="5,2" d="M373.769,-850.43C394.2739,-836.7396 422.6734,-817.778 444.9288,-802.9188"></path>
<polygon fill="#000000" stroke="#000000" points="447.0607,-805.7039 453.4339,-797.2402 443.1737,-799.8822 447.0607,-805.7039"></polygon>
</g>
<!-- crypto_framer.ProcessInput(data, Perspective::IS_SERVER) -->
<g id="node18" class="node">
<title>crypto_framer.ProcessInput(data, Perspective::IS_SERVER)</title>
<ellipse fill="none" stroke="#000000" cx="601.8545" cy="-706.6" rx="246.4224" ry="18"></ellipse>
<text text-anchor="middle" x="601.8545" y="-702.4" font-family="Times,serif" font-size="14.00" fill="#000000">crypto_framer.ProcessInput(data, Perspective::IS_SERVER)</text>
</g>
<!-- crypto_framer.set_visitor(this)&#45;&gt;crypto_framer.ProcessInput(data, Perspective::IS_SERVER) -->
<g id="edge22" class="edge">
<title>crypto_framer.set_visitor(this)-&gt;crypto_framer.ProcessInput(data, Perspective::IS_SERVER)</title>
<path fill="none" stroke="#000000" d="M509.3873,-761.9287C525.4813,-752.2987 545.5996,-740.2607 562.9569,-729.8748"></path>
<polygon fill="#000000" stroke="#000000" points="564.9621,-732.7537 571.7461,-724.6157 561.3678,-726.7469 564.9621,-732.7537"></polygon>
</g>
<!-- crypto_framer.ProcessInput(data, Perspective::IS_SERVER)&#45;&gt;visitor_&#45;&gt;OnStreamFrame(frame) -->
<g id="edge23" class="edge">
<title>crypto_framer.ProcessInput(data, Perspective::IS_SERVER)-&gt;visitor_-&gt;OnStreamFrame(frame)</title>
<path fill="none" stroke="#000000" stroke-dasharray="5,2" d="M617.7723,-724.6991C645.8668,-758.9318 698.0137,-834.3872 661.8545,-886.4 652.6021,-899.709 620.3122,-912.4593 588.2449,-922.3267"></path>
<polygon fill="#000000" stroke="#000000" points="586.9033,-919.0747 578.3259,-925.2938 588.9095,-925.781 586.9033,-919.0747"></polygon>
<text text-anchor="middle" x="703.2006" y="-819.8" font-family="Times,serif" font-size="14.00" fill="#000000">return true</text>
</g>
<!-- Process(input, perspective) -->
<g id="node19" class="node">
<title>Process(input, perspective)</title>
<ellipse fill="none" stroke="#000000" cx="601.8545" cy="-617.8" rx="115.7271" ry="18"></ellipse>
<text text-anchor="middle" x="601.8545" y="-613.6" font-family="Times,serif" font-size="14.00" fill="#000000">Process(input, perspective)</text>
</g>
<!-- crypto_framer.ProcessInput(data, Perspective::IS_SERVER)&#45;&gt;Process(input, perspective) -->
<g id="edge24" class="edge">
<title>crypto_framer.ProcessInput(data, Perspective::IS_SERVER)-&gt;Process(input, perspective)</title>
<path fill="none" stroke="#000000" stroke-dasharray="5,2" d="M600.6405,-688.2941C600.3175,-682.6251 600.0143,-676.3566 599.8545,-670.6 599.6473,-663.1362 599.6473,-661.2638 599.8545,-653.8 599.9219,-651.3714 600.0149,-648.8517 600.124,-646.3244"></path>
<polygon fill="#000000" stroke="#000000" points="603.6312,-646.2699 600.6405,-636.1059 596.6401,-645.9165 603.6312,-646.2699"></polygon>
</g>
<!-- Process(input, perspective)&#45;&gt;crypto_framer.ProcessInput(data, Perspective::IS_SERVER) -->
<g id="edge25" class="edge">
<title>Process(input, perspective)-&gt;crypto_framer.ProcessInput(data, Perspective::IS_SERVER)</title>
<path fill="none" stroke="#000000" stroke-dasharray="5,2" d="M601.8545,-635.872C601.8545,-648.0243 601.8545,-664.2958 601.8545,-678.168"></path>
<polygon fill="#000000" stroke="#000000" points="598.3546,-678.2005 601.8545,-688.2006 605.3546,-678.2006 598.3546,-678.2005"></polygon>
<text text-anchor="middle" x="631.2006" y="-658" font-family="Times,serif" font-size="14.00" fill="#000000">return true</text>
</g>
<!-- visitor_&#45;&gt;OnHandshakeMessage(message_) -->
<g id="node20" class="node">
<title>visitor_-&gt;OnHandshakeMessage(message_)</title>
<ellipse fill="none" stroke="#000000" cx="601.8545" cy="-529" rx="181.8167" ry="18"></ellipse>
<text text-anchor="middle" x="601.8545" y="-524.8" font-family="Times,serif" font-size="14.00" fill="#000000">visitor_-&gt;OnHandshakeMessage(message_)</text>
</g>
<!-- Process(input, perspective)&#45;&gt;visitor_&#45;&gt;OnHandshakeMessage(message_) -->
<g id="edge26" class="edge">
<title>Process(input, perspective)-&gt;visitor_-&gt;OnHandshakeMessage(message_)</title>
<path fill="none" stroke="#000000" stroke-dasharray="5,2" d="M600.6405,-599.4941C600.3175,-593.8251 600.0143,-587.5566 599.8545,-581.8 599.6473,-574.3362 599.6473,-572.4638 599.8545,-565 599.9219,-562.5714 600.0149,-560.0517 600.124,-557.5244"></path>
<polygon fill="#000000" stroke="#000000" points="603.6312,-557.4699 600.6405,-547.3059 596.6401,-557.1165 603.6312,-557.4699"></polygon>
</g>
<!-- visitor_&#45;&gt;OnHandshakeMessage(message_)&#45;&gt;Process(input, perspective) -->
<g id="edge27" class="edge">
<title>visitor_-&gt;OnHandshakeMessage(message_)-&gt;Process(input, perspective)</title>
<path fill="none" stroke="#000000" stroke-dasharray="5,2" d="M601.8545,-547.072C601.8545,-559.2243 601.8545,-575.4958 601.8545,-589.368"></path>
<polygon fill="#000000" stroke="#000000" points="598.3546,-589.4005 601.8545,-599.4006 605.3546,-589.4006 598.3546,-589.4005"></polygon>
<text text-anchor="middle" x="677.864" y="-569.2" font-family="Times,serif" font-size="14.00" fill="#000000">return QUIC_NO_ERROR</text>
</g>
<!-- void ChloFramerVisitor::OnHandshakeMessage -->
<g id="node21" class="node">
<title>void ChloFramerVisitor::OnHandshakeMessage</title>
<ellipse fill="none" stroke="#000000" cx="474.8545" cy="-456" rx="197.9146" ry="18"></ellipse>
<text text-anchor="middle" x="474.8545" y="-451.8" font-family="Times,serif" font-size="14.00" fill="#000000">void ChloFramerVisitor::OnHandshakeMessage</text>
</g>
<!-- visitor_&#45;&gt;OnHandshakeMessage(message_)&#45;&gt;void ChloFramerVisitor::OnHandshakeMessage -->
<g id="edge28" class="edge">
<title>visitor_-&gt;OnHandshakeMessage(message_)-&gt;void ChloFramerVisitor::OnHandshakeMessage</title>
<path fill="none" stroke="#000000" stroke-dasharray="5,2" d="M570.7868,-511.1422C554.0278,-501.5091 533.1489,-489.5078 515.1527,-479.1635"></path>
<polygon fill="#000000" stroke="#000000" points="516.4557,-475.8755 506.0416,-473.9265 512.9672,-481.9444 516.4557,-475.8755"></polygon>
</g>
<!-- delegate_&#45;&gt;OnChlo(framer_&#45;&gt;transport_version(), connection_id_, message) -->
<g id="node22" class="node">
<title>delegate_-&gt;OnChlo(framer_-&gt;transport_version(), connection_id_, message)</title>
<ellipse fill="none" stroke="#000000" cx="391.8545" cy="-383" rx="308.6845" ry="18"></ellipse>
<text text-anchor="middle" x="391.8545" y="-378.8" font-family="Times,serif" font-size="14.00" fill="#000000">delegate_-&gt;OnChlo(framer_-&gt;transport_version(), connection_id_, message)</text>
</g>
<!-- void ChloFramerVisitor::OnHandshakeMessage&#45;&gt;delegate_&#45;&gt;OnChlo(framer_&#45;&gt;transport_version(), connection_id_, message) -->
<g id="edge29" class="edge">
<title>void ChloFramerVisitor::OnHandshakeMessage-&gt;delegate_-&gt;OnChlo(framer_-&gt;transport_version(), connection_id_, message)</title>
<path fill="none" stroke="#000000" stroke-dasharray="5,2" d="M454.3376,-437.9551C443.9625,-428.8299 431.2129,-417.6165 419.9422,-407.7036"></path>
<polygon fill="#000000" stroke="#000000" points="422.2434,-405.0665 412.423,-401.0904 417.6205,-410.3228 422.2434,-405.0665"></polygon>
</g>
<!-- found_chlo_ = true -->
<g id="node23" class="node">
<title>found_chlo_ = true</title>
<ellipse fill="none" stroke="#000000" cx="728.8545" cy="-310" rx="85.2745" ry="18"></ellipse>
<text text-anchor="middle" x="728.8545" y="-305.8" font-family="Times,serif" font-size="14.00" fill="#000000">found_chlo_ = true</text>
</g>
<!-- delegate_&#45;&gt;OnChlo(framer_&#45;&gt;transport_version(), connection_id_, message)&#45;&gt;found_chlo_ = true -->
<g id="edge30" class="edge">
<title>delegate_-&gt;OnChlo(framer_-&gt;transport_version(), connection_id_, message)-&gt;found_chlo_ = true</title>
<path fill="none" stroke="#000000" d="M472.1462,-365.6074C529.145,-353.2605 604.8976,-336.8512 659.318,-325.0628"></path>
<polygon fill="#000000" stroke="#000000" points="660.1895,-328.4553 669.2219,-322.9175 658.7075,-321.6139 660.1895,-328.4553"></polygon>
</g>
<!-- ChloExtractor::Delegate::OnChlo(version, connection_id, chlo) -->
<g id="node24" class="node">
<title>ChloExtractor::Delegate::OnChlo(version, connection_id, chlo)</title>
<ellipse fill="none" stroke="#000000" cx="368.8545" cy="-310" rx="257.448" ry="18"></ellipse>
<text text-anchor="middle" x="368.8545" y="-305.8" font-family="Times,serif" font-size="14.00" fill="#000000">ChloExtractor::Delegate::OnChlo(version, connection_id, chlo)</text>
</g>
<!-- delegate_&#45;&gt;OnChlo(framer_&#45;&gt;transport_version(), connection_id_, message)&#45;&gt;ChloExtractor::Delegate::OnChlo(version, connection_id, chlo) -->
<g id="edge32" class="edge">
<title>delegate_-&gt;OnChlo(framer_-&gt;transport_version(), connection_id_, message)-&gt;ChloExtractor::Delegate::OnChlo(version, connection_id, chlo)</title>
<path fill="none" stroke="#000000" stroke-dasharray="5,2" d="M386.1691,-364.9551C383.5982,-356.7951 380.5011,-346.9652 377.6413,-337.8887"></path>
<polygon fill="#000000" stroke="#000000" points="380.8976,-336.5764 374.5542,-328.0904 374.2211,-338.68 380.8976,-336.5764"></polygon>
</g>
<!-- found_chlo_ = true&#45;&gt;visitor_&#45;&gt;OnHandshakeMessage(message_) -->
<g id="edge31" class="edge">
<title>found_chlo_ = true-&gt;visitor_-&gt;OnHandshakeMessage(message_)</title>
<path fill="none" stroke="#000000" stroke-dasharray="5,2" d="M728.0569,-328.0109C725.7508,-360.5079 716.7765,-429.6828 681.8545,-474 672.0135,-486.4885 658.562,-497.2416 645.4972,-505.8423"></path>
<polygon fill="#000000" stroke="#000000" points="643.6114,-502.8937 637.0094,-511.1801 647.3379,-508.8193 643.6114,-502.8937"></polygon>
</g>
<!-- ChloAlpnExtractor::OnChlo(version, connection_id, chlo) -->
<g id="node25" class="node">
<title>ChloAlpnExtractor::OnChlo(version, connection_id, chlo)</title>
<ellipse fill="none" stroke="#000000" cx="236.8545" cy="-237" rx="236.7091" ry="18"></ellipse>
<text text-anchor="middle" x="236.8545" y="-232.8" font-family="Times,serif" font-size="14.00" fill="#000000">ChloAlpnExtractor::OnChlo(version, connection_id, chlo)</text>
</g>
<!-- ChloExtractor::Delegate::OnChlo(version, connection_id, chlo)&#45;&gt;ChloAlpnExtractor::OnChlo(version, connection_id, chlo) -->
<g id="edge33" class="edge">
<title>ChloExtractor::Delegate::OnChlo(version, connection_id, chlo)-&gt;ChloAlpnExtractor::OnChlo(version, connection_id, chlo)</title>
<path fill="none" stroke="#000000" stroke-dasharray="5,2" d="M336.5637,-292.1422C318.9851,-282.4207 297.0454,-270.2874 278.2251,-259.8792"></path>
<polygon fill="#000000" stroke="#000000" points="279.7143,-256.7032 269.2695,-254.9265 276.3266,-262.8289 279.7143,-256.7032"></polygon>
</g>
<!-- rejector_&#45;&gt;OnChlo -->
<g id="node26" class="node">
<title>rejector_-&gt;OnChlo</title>
<ellipse fill="none" stroke="#000000" cx="368.8545" cy="-164" rx="84.6831" ry="18"></ellipse>
<text text-anchor="middle" x="368.8545" y="-159.8" font-family="Times,serif" font-size="14.00" fill="#000000">rejector_-&gt;OnChlo</text>
</g>
<!-- ChloAlpnExtractor::OnChlo(version, connection_id, chlo)&#45;&gt;rejector_&#45;&gt;OnChlo -->
<g id="edge34" class="edge">
<title>ChloAlpnExtractor::OnChlo(version, connection_id, chlo)-&gt;rejector_-&gt;OnChlo</title>
<path fill="none" stroke="#000000" d="M269.1453,-219.1422C287.331,-209.085 310.184,-196.4466 329.4221,-185.8073"></path>
<polygon fill="#000000" stroke="#000000" points="331.2003,-188.8235 338.2574,-180.9211 327.8126,-182.6979 331.2003,-188.8235"></polygon>
</g>
<!-- rejector_&#45;&gt;OnChlo&#45;&gt;ChloExtractor::Delegate::OnChlo(version, connection_id, chlo) -->
<g id="edge35" class="edge">
<title>rejector_-&gt;OnChlo-&gt;ChloExtractor::Delegate::OnChlo(version, connection_id, chlo)</title>
<path fill="none" stroke="#000000" stroke-dasharray="5,2" d="M425.5556,-177.3991C447.3536,-185.6133 470.0599,-198.6213 482.8545,-219 491.3622,-232.5506 491.3622,-241.4494 482.8545,-255 473.7707,-269.4683 459.6907,-280.2214 444.4868,-288.1813"></path>
<polygon fill="#000000" stroke="#000000" points="442.9177,-285.0523 435.4344,-292.5523 445.9615,-291.3559 442.9177,-285.0523"></polygon>
</g>
<!-- void StatelessRejector::OnChlo -->
<g id="node27" class="node">
<title>void StatelessRejector::OnChlo</title>
<ellipse fill="none" stroke="#000000" cx="288.8545" cy="-91" rx="133.1388" ry="18"></ellipse>
<text text-anchor="middle" x="288.8545" y="-86.8" font-family="Times,serif" font-size="14.00" fill="#000000">void StatelessRejector::OnChlo</text>
</g>
<!-- rejector_&#45;&gt;OnChlo&#45;&gt;void StatelessRejector::OnChlo -->
<g id="edge36" class="edge">
<title>rejector_-&gt;OnChlo-&gt;void StatelessRejector::OnChlo</title>
<path fill="none" stroke="#000000" stroke-dasharray="5,2" d="M349.4887,-146.3287C339.5162,-137.2288 327.1874,-125.9787 316.2564,-116.0042"></path>
<polygon fill="#000000" stroke="#000000" points="318.3438,-113.1708 308.5977,-109.0157 313.6254,-118.3416 318.3438,-113.1708"></polygon>
</g>
<!-- 设置state_, 拷贝握手消息，很简单 -->
<g id="node28" class="node">
<title>设置state_, 拷贝握手消息，很简单</title>
<ellipse fill="none" stroke="#000000" cx="368.8545" cy="-18" rx="162.0983" ry="18"></ellipse>
<text text-anchor="middle" x="368.8545" y="-13.8" font-family="Times,serif" font-size="14.00" fill="#000000">设置state_, 拷贝握手消息，很简单</text>
</g>
<!-- void StatelessRejector::OnChlo&#45;&gt;设置state_, 拷贝握手消息，很简单 -->
<g id="edge37" class="edge">
<title>void StatelessRejector::OnChlo-&gt;设置state_, 拷贝握手消息，很简单</title>
<path fill="none" stroke="#000000" stroke-dasharray="5,2" d="M308.6298,-72.9551C318.5338,-63.9177 330.6826,-52.8319 341.4683,-42.9899"></path>
<polygon fill="#000000" stroke="#000000" points="344.0018,-45.4163 349.0294,-36.0904 339.2834,-40.2455 344.0018,-45.4163"></polygon>
</g>
<!-- 设置state_, 拷贝握手消息，很简单&#45;&gt;rejector_&#45;&gt;OnChlo -->
<g id="edge38" class="edge">
<title>设置state_, 拷贝握手消息，很简单-&gt;rejector_-&gt;OnChlo</title>
<path fill="none" stroke="#000000" stroke-dasharray="5,2" d="M398.3849,-35.9405C410.8676,-45.2769 424.0683,-57.9085 430.8545,-73 437.4163,-87.5926 437.4163,-94.4074 430.8545,-109 425.2587,-121.4444 415.3013,-132.2161 404.9814,-140.8468"></path>
<polygon fill="#000000" stroke="#000000" points="402.8066,-138.1044 397.0853,-147.0217 407.1187,-143.6185 402.8066,-138.1044"></polygon>
</g>
</g>
</svg>

2.
QuicDispatcher::OnUnauthenticatedHeader永远返回false。所以在visitor_->OnUnauthenticatedHeader会直接退出ProcessPacket。

# visitor_的设置
1. void QuicSession::Initialize()的时候，会将connection的visitor设置成 this  connection_->set_visitor(this); 此时指向的是QuicSession.
2. QuicConnection在初始化时就设置了framer_的visitor_.
framer_.set_visitor(this); 这样只要ProcessUdpPacket函数的framer_.ProcessPacket都会调用QuicConnection的函数。

# 变量统计
## QuicConnectionStats::QuicConnectionStats()
收发的包量，丢包数，重传数等，基于连接,stream的统计
```
bytes_sent(0),
      packets_sent(0),
      stream_bytes_sent(0),
      packets_discarded(0),
      bytes_received(0),
      packets_received(0),
      packets_processed(0),
      stream_bytes_received(0),
      bytes_retransmitted(0),
      packets_retransmitted(0),
      bytes_spuriously_retransmitted(0),
      packets_spuriously_retransmitted(0),
      packets_lost(0),
      slowstart_packets_sent(0),
      slowstart_packets_lost(0),
      slowstart_bytes_lost(0),
      packets_dropped(0),
      crypto_retransmit_count(0),
      loss_timeout_count(0),
      tlp_count(0),
      rto_count(0),
      min_rtt_us(0),
      srtt_us(0),
      max_packet_size(0),
      max_received_packet_size(0),
      estimated_bandwidth(QuicBandwidth::Zero()),
      packets_reordered(0),
      max_sequence_reordering(0),
        max_time_reordering_us(0),
        tcp_loss_events(0),
        connection_creation_time(QuicTime::Zero()),
        blocked_frames_received(0),
        blocked_frames_sent(0) {}

```
# 连接迁移
## 判断是否连接迁移
bool QuicConnection::OnPacketHeader(const QuicPacketHeader& header)的时候会调用
QuicUtils::DetermineAddressChangeType来判断变化类型，有如下几种：
```

  if (old_address.host() == new_address.host()) {
    return PORT_CHANGE;
  }

  bool old_ip_is_ipv4 = old_address.host().IsIPv4() ? true : false;
  bool migrating_ip_is_ipv4 = new_address.host().IsIPv4() ? true : false;
  if (old_ip_is_ipv4 && !migrating_ip_is_ipv4) {
    return IPV4_TO_IPV6_CHANGE;
  }

  if (!old_ip_is_ipv4) {
    return migrating_ip_is_ipv4 ? IPV6_TO_IPV4_CHANGE : IPV6_TO_IPV6_CHANGE;
  }

  const int kSubnetMaskLength = 24;
  if (old_address.host().InSameSubnet(new_address.host(), kSubnetMaskLength)) {
    // Subnet part does not change (here, we use /24), which is considered to be
    // caused by NATs.
    return IPV4_SUBNET_CHANGE;
  }

  return IPV4_TO_IPV4_CHANGE;
```
## void QuicConnection::UpdatePacketContent(PacketContent type)
```
// Start peer migration immediately when the current packet is confirmed not
// a connectivity probing packet.
StartPeerMigration(current_peer_migration_type_);
current_peer_migration_type_ = NO_CHANGE;
```
