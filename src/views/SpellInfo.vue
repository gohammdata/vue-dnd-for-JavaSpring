<template>
    <div v-if="!loading" class="monsterinfo">
        <v-card
            class="mx-auto"
            outlined
        >
            <!-- Header -->
            <v-list-item three-line class="text-left">
                <v-list-item-content>
                    <div class="overline mb-4">SPELL</div>
                    <v-list-item-title class="headline mb-1">
                        {{ s.name }}
                        <v-spacer></v-spacer>
                        <div v-if="s.level==0">
                            Cantrip
                        </div>
                        <div v-else>
                            Level {{ s.level }} Spell
                        </div>
                    </v-list-item-title>
                    <v-list-item-subtitle>{{ s.book }} p. {{ s.page}} </v-list-item-subtitle>
                </v-list-item-content>
            </v-list-item>
            <!-- Description -->
            <v-list-item three-line class="text-left">
                <v-list-item-content>
                    <div class="overline mb-4">DESCRIPTION</div>
                    <p class="spell-desc"><span v-html="s.desc"></span></p>
                </v-list-item-content>
            </v-list-item>
            <!-- Casting and Components -->
            <v-list-item three-line class="text-left">
                <v-list-item-content>
                    <div class="overline mb-4">CASTING</div>
                    <div class="casting">
                    <v-simple-table class="casting-table">
                        <tbody>
                            <tr>
                                <td>Cast Range (ft)</td>
                                <td>{{ s.rangeString }}</td>
                            </tr>
                            <tr>
                                <td>Cast Time (s)</td>
                                <td>{{ s.castTimeString }}</td>
                            </tr>
                            <tr>
                                <td>Duration</td>
                                <td>{{ s.durationsByComma }}</td>
                            </tr>
                            <tr>
                                <td>Classes</td>
                                <td>{{ s.classesByComma }}</td>
                            </tr>
                        </tbody>
                    </v-simple-table>
                    </div>
                </v-list-item-content>
                <v-list-item-content>
                    <div class="overline mb-4">COMPONENTS</div>
                    <p>{{ s.components.raw }}</p>
                    <p v-if="s.components.materials_needed">{{ s.components.materials_needed }}</p>
                </v-list-item-content>
            </v-list-item> 
        </v-card>
    </div>
    <div v-else>
        <v-overlay>
            <v-progress-circular
                indeterminate
                size="64"
            ></v-progress-circular>
        </v-overlay>
    </div>
</template>

<script lang="ts">
import Vue from 'vue'
import Component from 'vue-class-component';
import { Prop } from 'vue-property-decorator';
import Spell, { SpellDTO } from '@/models/Spell';
import { SpellApi } from '@/api/SpellApi';

@Component({
    filters: {
        capitalize(value: string) {
            return value.replace(/\b\w/g, l => l.toUpperCase())
        }
    }
 })
export default class SpellInfo extends Vue { 
    @Prop() private id!: string;
    private s!: Spell;
    private loading:boolean = false;
    async mounted():Promise<void> {
        this.loading = !this.loading;
        this.s = await SpellApi.getSpell(this.id);
        this.loading = !this.loading;
    }
    data() {
      return {
        s: Spell
      }
    }
}
</script>

<style lang="scss">
p.spell-desc {
    table {
        border-collapse: collapse;
        border: 1px solid;
        margin-bottom: 15px;
    }
    table, th, td {
        border: 1px solid black;
        padding: 2px;
    }
}
div.casting-table {
    margin: 0px;
}
</style>
