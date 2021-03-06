<!-- OpenStudio(R), Copyright (c) 2008-2016, Alliance for Sustainable Energy, LLC. All rights reserved.
Redistribution and use in source and binary forms, with or without modification, are permitted provided that the following conditions are met:
(1) Redistributions of source code must retain the above copyright notice, this list of conditions and the following disclaimer.
(2) Redistributions in binary form must reproduce the above copyright notice, this list of conditions and the following disclaimer in the documentation and/or other materials provided with the distribution.
(3) Neither the name of the copyright holder nor the names of any contributors may be used to endorse or promote products derived from this software without specific prior written permission from the respective party.
(4) Other than as required in clauses (1) and (2), distributions in any form of modifications or other derivative works may not use the "OpenStudio" trademark, "OS", "os", or any other confusingly similar designation without specific prior written permission from Alliance for Sustainable Energy, LLC.
THIS SOFTWARE IS PROVIDED BY THE COPYRIGHT HOLDERS AND CONTRIBUTORS "AS IS" AND ANY EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT LIMITED TO, THE IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS FOR A PARTICULAR PURPOSE ARE DISCLAIMED. IN NO EVENT SHALL THE COPYRIGHT HOLDER, THE UNITED STATES GOVERNMENT, OR ANY CONTRIBUTORS BE LIABLE FOR ANY DIRECT, INDIRECT, INCIDENTAL, SPECIAL, EXEMPLARY, OR CONSEQUENTIAL DAMAGES (INCLUDING, BUT NOT LIMITED TO, PROCUREMENT OF SUBSTITUTE GOODS OR SERVICES; LOSS OF USE, DATA, OR PROFITS; OR BUSINESS INTERRUPTION) HOWEVER CAUSED AND ON ANY THEORY OF LIABILITY, WHETHER IN CONTRACT, STRICT LIABILITY, OR TORT (INCLUDING NEGLIGENCE OR OTHERWISE) ARISING IN ANY WAY OUT OF THE USE OF THIS SOFTWARE, EVEN IF ADVISED OF THE POSSIBILITY OF SUCH DAMAGE. -->

<template>
<section id="inspector">
    <section id="geometry-list" class="list">
        <template v-if="!currentSpace && !currentShading" v-for="face in currentStoryGeometry.faces">
            <header>
                <h3>Face {{face.id}}</h3>
            </header>
            <div v-for="edgeRef in face.edgeRefs" class="list-item">
                edge {{ edgeRef.edge_id }} {{ edgeRef.reverse ? 'reversed ' : '' }}
                <br>startpoint {{ startpoint(edgeRef) }}
                <br>endpoint {{ endpoint(edgeRef) }}
            </div>
        </template>

        <template v-if="currentSelectionsFace">
            <header>
                <h3>Face {{currentSelectionsFace.id}} on {{ currentSpace ? currentSpace.name : currentShading.name }}</h3>
            </header>
            <div v-for="edgeRef in currentSelectionsFace.edgeRefs" class="list-item">
                edge {{ edgeRef.edge_id }} {{ edgeRef.reverse ? 'reversed ' : '' }}
                <br>startpoint {{ startpoint(edgeRef) }}
                <br>endpoint {{ endpoint(edgeRef) }}
            </div>
        </template>
    </section>
</section>
</template>

<script>

import { mapState } from 'vuex'
import geometryHelpers from './../store/modules/geometry/helpers'
import modelHelpers from './../store/modules/models/helpers'

export default {
    name: 'inspector',
    data() {
        return {
            geometryInspector: true,
            storyInspector: false
        }
    },
    methods: {
		startpoint (edgeRef) {
            const edge = this.edgeForId(edgeRef.edge_id);
            if (edgeRef.reverse) {
                const vertex = this.vertexForId(edge.v2);
                return 'v2 ' + vertex.id + ' (' + vertex.x + ',' + vertex.y + ')';
            } else {
                const vertex = this.vertexForId(edge.v1);
                return 'v1 ' + vertex.id + ' (' + vertex.x + ',' + vertex.y + ')';
            }
        },
        endpoint (edgeRef) {
            const edge = this.edgeForId(edgeRef.edge_id);
            if (!edgeRef.reverse) {
                const vertex = this.vertexForId(edge.v2);
                return vertex.id + ' (v2) ' + ' (' + vertex.x + ',' + vertex.y + ')';
            } else {
                const vertex = this.vertexForId(edge.v1);
                return vertex.id + ' (v1) ' + ' (' + vertex.x + ',' + vertex.y + ')';
            }
        },
        edgeForId (edge_id) { return geometryHelpers.edgeForId(edge_id, this.currentStoryGeometry); },
        vertexForId (vertex_id) {
            const vertex = geometryHelpers.vertexForId(vertex_id, this.currentStoryGeometry);
            return vertex;
        }
    },
    computed: {
        currentSelectionsFace () { return this.$store.getters['application/currentSelectionsFace']; },
        currentStoryGeometry () { return this.$store.getters['application/currentStoryGeometry']; },
        ...mapState({
            currentStory: state => state.application.currentSelections.story,
            currentSpace: state => state.application.currentSelections.space,
            currentShading: state => state.application.currentSelections.shading
        })
    },
}
</script>

<style lang="scss" scoped>
@import "./../scss/config";
    #inspector {
        // background-color: $gray-medium-dark;
        background-color: $gray-medium;
        border-left: 1px solid $gray-darkest;
        font-size: .85rem;
		width: 18rem;
	    position: absolute;
	    right: 0;
	    z-index: 2;
	    top: 0;

        .list {
            height: calc(100vh - 4.75rem);
            overflow: scroll;

            header {
                background-color: $gray-dark;
                > h3 {
                    display: inline-block;
                    margin: 1rem;
                }
            }
            .list-item {
                border-top: 1px solid $gray-medium-light;
                padding: .5rem 1rem;
                h4 {
                    margin: .5rem 0;
                }
                span {
                    display: block;
                    margin-bottom: .25rem;
                }
                button {
                    margin-top: .5rem;
                }
            }
        }
    }
</style>
